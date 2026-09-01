---
layout: post
title: "How I Found Every Berlin Postleitzahl Inside the Ring"
date: 2026-09-01 11:05:00 +0200
tags: [geospatial, berlin, openstreetmap, data]
categories: [technology]
description: "Berlin's Ringbahn has no official 'inside', so I pulled the train's geometry from OpenStreetMap, turned it into a polygon, and scored all 185 postal codes against it. Full method, map, and every number."
---

# How I Found Every Berlin Postleitzahl Inside the Ring

*2026-09-01 · 6 min read · [geospatial] [berlin] [openstreetmap] [data]*

I wanted a simple answer: which of Berlin's postal codes live inside the Ringbahn, the circular S-Bahn loop that rings the center of the city. I expected a five minute Google search. I found no official list, because "inside the ring" is not a legal boundary. Nobody draws it. So I pulled the train's actual geometry from OpenStreetMap, closed it into a polygon, and measured every postal code against it. Here is the whole method, the map, and every single number.

> **The short version:** There are 185 postal codes in Berlin. I took the S-Bahn ring as a shape (86.8 square km), took each postal code as a shape, and computed how much of each code falls inside the ring. **40 codes sit entirely inside.** A further **19 are mostly inside** (the ring clips just a corner). **9 straddle the line**, and **8 only touch it.** So the strict answer is 40; the everyday answer, the one where you still call the place "in the ring," is 59.

## The question, and why it has no official answer

A postal code (*Postleitzahl*, PLZ) is an administrative region. The city draws its boundaries, but the boundaries are fuzzy. Rivers, parkland, and railway corridors are often not assigned to any code, and adjacent codes do not always meet cleanly. The Ringbahn itself is a 37 km loop of track. It is a line, not a wall. A postal code can be cut in two by it.

So "is PLZ X inside the ring" does not have a yes or no answer that any authority will print. It only has a *geometric* answer: how much of that code's area lies on the inside of the loop. That is a question a computer can answer, which is why I went looking for the data.

## Step 1: Turn the ring into a shape

The Ringbahn runs as the S41 and S42 lines. In OpenStreetMap that is a single [route relation](https://www.openstreetmap.org/relation/14981) (id 14981). A route relation is a list of ways, the individual line segments the train follows. I fetched that list from the [Overpass API](https://overpass-turbo.eu/) (a query interface for OpenStreetMap data), chained the segments end to end into one closed loop, and filled it in.

The result is a polygon. It encloses **86.8 square kilometers**, which is a sensible number for the area the ring wraps around. That polygon is the "inside the ring" region for everything that follows.

## Step 2: Turn every postal code into a shape

OpenStreetMap also maps postal codes as regions, as `postal_code` boundary relations. I pulled all of them that fall in Berlin's bounding box. **185 of them are Berlin codes.** Each one is a polygon, sometimes several polygons with holes (a code can have an island, or wrap a hole). I reconstructed each code into a proper filled shape the same way: chain the boundary lines, close them, fill.

## Step 3: Intersect and score

Now it is plain polygon math. For each of the 185 postal code shapes I compute the overlap with the ring shape, using the [Shapely](https://shapely.readthedocs.io/en/stable/) library in Python:

```python
from shapely.geometry import shape
frac_inside = plz_polygon.intersection(ring_polygon).area / plz_polygon.area
```

That fraction tells me how much of the code is on the inside. 1.0 means the whole code is inside. 0.4 means the ring cuts through it and a bit under half is in. I sort all 185 by that number and split them into four tiers.

## The result: four tiers

**A. Entirely inside the ring (99.9% or more), 40 codes:**

10115, 10117, 10119, 10178, 10179, 10243, 10435, 10555, 10557, 10585, 10587, 10623, 10625, 10627, 10629, 10707, 10717, 10719, 10777, 10779, 10781, 10783, 10785, 10787, 10789, 10823, 10825, 10961, 10963, 10965, 10967, 10969, 10997, 10999, 12043, 12045, 12047, 12049, 12053, 13355

**B. Mostly inside, the ring clips a corner (50% to 99.9%), 19 codes:**

10245 (54%), 10247 (89%), 10249 (93%), 10405 (99%), 10437 (96%), 10551 (98%), 10553 (70%), 10559 (98%), 10589 (76%), 10709 (98%), 10711 (64%), 10713 (99%), 10715 (97%), 10827 (88%), 12051 (61%), 12055 (57%), 12059 (90%), 12101 (100%), 14059 (65%)

**C. Straddles the ring, mostly outside (under 50%), 9 codes:**

10407 (29%), 10439 (7%), 10829 (43%), 12099 (23%), 12435 (33%), 13347 (20%), 13353 (14%), 13357 (23%), 14057 (42%)

**D. Only touches the ring (5% or less), effectively outside, 8 codes:**

10365, 10369, 10409, 12057, 12103, 12159, 14055, 14197

The strict answer is tier A: **40 postal codes**. The everyday answer, tier A plus tier B, is **59**. Anyone who tells you "I live in the ring" and whose code is in tier B is telling the truth in the way people mean it.

## What the white gaps are

On the map below, some areas inside the ring are white, colored by no code at all. That is not a bug in my math. It is the fuzzy-boundary problem from the first section, made visible. The white space is water and infrastructure that no postal code claims: the Spree and its side canals, the railway corridors the train itself runs on, and small seams where two adjacent codes do not quite meet. Roughly a third of the ring's area is like this, and it functionally belongs to the codes that border it.

## The map

Green is tier A (entirely inside), yellow is tier B (mostly inside), red is tier C (straddling). The black loop is the S41/S42 ring.

![Berlin postal codes inside the Ringbahn: green entirely inside, yellow mostly inside, red straddling, black ring loop](/assets/images/plz-ringbahn/ringbahn-map.svg)

The image is an SVG, so you can zoom into it without losing detail. Open it in a browser and it stays crisp at any size.

## Where you would start

This is a small problem, but it has the shape of a lot of "is X inside Y" questions: a fuzzy region, a set of candidate shapes, and a computer that will happily do the measuring. The reusable recipe is:

1. Find the region as a geometry. OpenStreetMap has a route relation for the ring and boundary relations for the codes.
2. Close each set of line segments into a filled polygon.
3. Intersect each candidate with the region and record the fraction of its area that falls inside.
4. Sort and tier the fractions.

The hard part is not the code. It is deciding what "inside" means, and accepting that the data will be a little messy at the edges. The numbers above are as good as the underlying map data, and the map data is good enough to be useful.
