---
layout: post
title: "How I Got LTX-2.3 Running in ComfyUI: Six Errors and a Lot of Patience"
date: 2026-08-04 02:44:00 +0200
description: "The full debugging journey of getting LTX-2.3 (22B video model) running on an 8 GB VRAM GPU: six distinct errors, their root causes, and the working workflow."
tags: [comfyui, ai, video, ltx, debugging, raspberry-pi]
categories: technology
---

# How I Got LTX-2.3 Running in ComfyUI: Six Errors and a Lot of Patience

In my last post I described how I turned my Raspberry Pi into a remote control for a Windows PC with an RTX 3070 Ti, generating a wobbly two-tailed cat video with AnimateDiff. The natural next step was to try a real video model. Not a motion module bolted onto an image model, but an actual text-to-video model: LTX-2.3, a 22B-parameter model from Lightricks that generates video and audio in one pass.

What followed was the most educational debugging session I have had in a while. Six distinct errors, each one hiding the next, each one teaching me something about how ComfyUI actually works under the hood.

## The Setup

Same hardware as last time:

| Machine | Role |
|---------|------|
| Raspberry Pi 5 | Brain: builds the workflow JSON, submits it via the ComfyUI API |
| Windows PC (RTX 3070 Ti, 8 GB VRAM) | Muscle: runs ComfyUI |

The model files: `ltx-2.3-22b-dev-fp8.safetensors` (22B params, fp8 quantized, 29 GB on disk), the Gemma text encoder, and the model's VAE.

## Error 1: "clip input is invalid: None"

The first submission failed immediately. LTX-2.3 does not bundle a text encoder inside its checkpoint, so the workflow tried to grab a CLIP model that was not there.

**Fix:** Load the text encoder separately. ComfyUI has a `CLIPLoader` node with a `type` parameter, and for LTX the correct value is `ltxv`, pointing at the Gemma encoder.

## Error 2: "Tensors must have same number of dimensions: got 4 and 3"

Now the model loaded, but the sampler exploded. This one took a while. The generic `CLIPLoader` produces text embeddings in a shape that LTX-2.3's video embedding connector cannot digest.

**Fix:** Use the node made for this exact purpose: `LTXAVTextEncoderLoader`. It knows how to pair the Gemma text encoder with the LTX checkpoint and produces embeddings the model accepts.

## Error 3: "'dict' object has no attribute 'sample'"

The workflow validated but crashed at runtime. My JSON embedded the sampler as an inline object instead of referencing a separate sampler node.

**Fix:** In the API format, every node must be a top-level entry, and connections use `["node_id", output_index]` references. The sampler got its own node, and the sampler node got a proper reference to it.

## Error 4: "expected input to have 16 channels, but got 128 channels"

This was the first clue about LTX-2.3's architecture. The VAE decoder expects 16 channels, but the sampler produced a latent with 128 channels. LTX-2.3 is a joint audio-video model: the latent contains both the video stream and the audio stream, interleaved.

**Fix:** Insert `LTXVSeparateAVLatent` between the sampler and the video decoder. It splits the joint latent into its video and audio halves.

## Error 5: "tuple index out of range"

The separator node complained it could not find the audio half. Right, because the workflow never created one. A joint AV model needs an audio latent to pair with the video latent, even when you only want the video.

**Fix:** Build the full audio chain: `LTXVAudioVAELoader` loads the audio VAE from the checkpoint, `LTXVEmptyLatentAudio` creates an empty audio latent, and `LTXVConcatAVLatent` merges the video and audio latents into the joint structure the sampler expects. After sampling, `LTXVSeparateAVLatent` splits them again.

## Error 6: the same 128-channel error, again

With the full AV chain in place, the sampler finally ran. The separator split the latent. And the decoder still choked on 128 channels.

The culprit was the VAE file itself. I had pointed the decoder at the separately downloaded `ae.safetensors`, which is LTX-2's VAE and expects 16 channels. LTX-2.3 is a different architecture with a 128-channel latent, and its VAE ships inside the checkpoint.

**Fix:** Take the VAE from the checkpoint loader's second output slot instead of loading a separate VAE file.

## The Working Recipe

The final workflow, for anyone who wants to skip the six hours of debugging:

```
CheckpointLoaderSimple (ltx-2.3-22b-dev-fp8)
├── model ──▶ CFGGuider ──▶ SamplerCustomAdvanced
├── vae (slot 2!) ──▶ VAEDecode
LTXAVTextEncoderLoader (gemma) ──▶ CLIPTextEncode ×2 ──▶ LTXVConditioning
EmptyLTXVLatentVideo ──┐
                       ├──▶ LTXVConcatAVLatent ──▶ SamplerCustomAdvanced
LTXVAudioVAELoader ──▶ LTXVEmptyLatentAudio ──┘
SamplerCustomAdvanced ──▶ LTXVSeparateAVLatent ──▶ VAEDecode ──▶ SaveAnimatedWEBP
```

Here is the result, 49 frames at 512x512, generated from the prompt "a cute orange cat walking through a sunlit park":

<video controls loop muted playsinline width="100%" style="max-width:512px; border-radius:8px;">
  <source src="/assets/videos/ltx23-cat.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

## What I Learned

- **LTX-2.3 is not a video model with an audio add-on.** It is a joint audio-video model. The latent is one interleaved structure, and every stage of the pipeline has to respect that: audio latent creation, concatenation before sampling, separation after.
- **The right node matters more than the right parameter.** Every error here was about wiring, not about values. When a ComfyUI workflow fails in a strange way, question the node choice before the settings.
- **Checkpoints can carry their own VAE.** The separate VAE file in my models folder looked correct but was for the wrong model version. The VAE that matches the model lives inside the checkpoint.
- **8 GB of VRAM will run a 22B model, technically.** The render took over 20 minutes and the GPU utilization hovered around 10%. It works, but it is the slowest way to generate a two-second clip. For this model, 12-16 GB of VRAM is the honest minimum for comfortable use.
- **Debugging through an API is a great teacher.** Because I drove ComfyUI from the Pi over its REST API, I had to read every error message, check every node interface, and understand the graph end to end. No clicking around in a UI hoping something works.

The two-tailed cat from last time has a new cousin now. Same cat, same park, same two tails, but this one came from a real 22-billion-parameter video model, rendered at 24 frames per second, through six errors and one very patient Raspberry Pi.
