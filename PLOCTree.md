# PLOCTree
A Fast, High-Quality Hardware BVH Builder

---

## Background
<!--
Let's start with some basic concepts which are required in order to understand the paper.
-->

---

### Bounding Volume Hierarchy (BVH)
Accelleration data structure for ray tracing.
<!--
In case you  ̶d̶o̶n̶'̶t̶ ̶k̶n̶o̶w̶ ̶a̶l̶r̶e̶a̶d̶y̶ are stupid, BVHs are kind of needed for raytracing...
... and this is relevant
... because raytracing is f***ing cool.
-->

---

<!-- 

  Paper: "Automatic Creation of Object Hierarchies for Ray Tracing"
Authors: Jeffrey Goldsmith and John Salmon
   Year: 1987
    
-->

![](https://i.imgur.com/dnjAbHX.png =600x)
<!-- 
So you use volumes like Axis Alligned Bounding Boxes (AABB:S) to enclose geometry primitives. AABB:s are one really common as the ray intersection test is really fast and easy to compute.
-->

---

![](https://i.imgur.com/uHKtdoK.png =600x)
<!-- 
The next step is to construct a hierachy of such boxes. 
-->

---

![](https://i.imgur.com/tasDKhh.png =600x)

<!-- 
This results in a tree of AABB:s which is used as accelleration data structure for the ray intersection tests reducing the cost form O(n) to O(logn).
-->

---

### Surface Area Heuristic (SAH)
<!-- Because optimal BVH construction is NP-hard. -->
Estimates the likelihood of intersection with a given tree node based on the surface area of its bounding box.

![](https://i.imgur.com/P49qPzB.png)

<!-- assumption: keeping thing close together in the same boundiong box is good -->

---

### Morton Code

![](https://upload.wikimedia.org/wikipedia/commons/c/cd/Four-level_Z.svg)

<!--
morton order maps two dimentions into a single dimention (array) while preserving approximate distancing

also, easy to compute from binary coordinates
-->

---

### Streaming algorithm


<!-- maybe move in backgound? -->

From [wikipedia](https://en.wikipedia.org/wiki/Streaming_algorithm#Data_stream_model): "streaming algorithms are algorithms for processing data streams in which the input is presented as a sequence of items and can be examined in only a few passes"

---

### BVH builders

---

#### SAH-based
Top-down construction via "SAH sweeps"

**PRO**: High quality
**CON**: Costly 

---

#### LBVH-based
Sort triangles according to their Morton Codes, and then produces a hierarchy based on that

**PRO**: Linear cost
**CON**: Inaccurate

---

#### Parallel Locality Ordered Clustering (PLOC)

Parallel, bottom-up BVH construction algorithm

Uses Morton Code order for efficient neighboring distance computation

---

##### The PLOC algorithm steps:

1. Assign each triangle a Morton Code based on its centroid
2. Sort triangles in Morton code order
3. Apply multiple "PLOC sweeps" <!-- iterative approach -->

---

##### PLOC sweeps

![](https://i.imgur.com/TSEUX7o.png)
![](https://i.imgur.com/8rgJUZP.png)

----

##### PLOC sweeps

![](https://i.imgur.com/hhuzRBo.png)
![](https://i.imgur.com/8rgJUZP.png)

----

##### PLOC sweeps

![](https://i.imgur.com/GE9nVsl.png)
![](https://i.imgur.com/8rgJUZP.png)

----

##### PLOC sweeps

![](https://i.imgur.com/YV5IpFE.png)
![](https://i.imgur.com/8rgJUZP.png)

---

##### Nearest Neighbors using Morton Code ordering

![](https://i.imgur.com/HZ4vajy.png =500x)
<!-- Illustration of the nearest neighbor search for r ¼ 2. Two clusters (red triangles) search for their nearest neighbors (blue triangles) in the neighborhood (red curve). Notice how the algorithm adapts to the den- sity of clusters in the neighborhood. -->

---

## PLOCTree

<!--

* Hardware implemented version of PLOC
* Compare with other HW implementations
* Compare with GPU PLOC

-->

---

### Algorithm adaptation
Changes to the PLOC algorithm have to be made on order for an hardware implementation

---

#### Basic, serial PLOC

<!-- should'n this be "Serial PLOC" ... -->
![](https://i.imgur.com/9MqpLKh.png =700x)

* All the data is required at start (line 1-3)
* Runs kernel multiple times

---

#### Streaming, hardware-oriented PLOC
<!-- ... and this "Streaming PLOC"? -->

* Fuses the two loops into a single one
* Exploits having a small execution window

---

### Hardware implementation
* Steam pipeline for a fixed-radius PLOC sweep
* System made out of several pipelines

---

#### PLOC sweep pipeline

---

![](https://i.imgur.com/XmzWalB.png)

---

#### System architecture

---

![](https://i.imgur.com/KiThMjf.png =700x)


---

#### Evaluation

---

<!-- maybe make a sentece out of this? -->

* RTL level in SystemVerilog
* 28nm, 1Ghz <!-- power analysis based on switching activity, revord build times, mem traffic, build energy-->
* Various scenes <!-- Fifteen test scenes, verify from RTL as hexdump and render using it-->
* Performance metrics <!-- SAH cost of output trees -->
* GPU PLOC and on 1080 Ti <!-- memory traffic extracted -->

---

## Results

---

<!-- 1-3W mobile gpu, nothing left for rendering -->
<!-- Run at full power on desktop -->
  
* PLOCTree consumes 1.4–1.9W 
* 7% slower than LBVH
* More power/energy consumtion than LBVH
* 5 x faster with 3 x less bandwith usage, 5 x less silicon than "binned SAH builder"
* 4 x faster and 8 x less bandwidth than CUDA PLOC implementation

---

![](https://i.imgur.com/3ozffhT.png)

---

![](https://i.imgur.com/AVXgN9x.png)

---

## Shortcomings

---

* Shaky performance metrics, not accurate - is performance really better than LBVH?
* Not acounting for newer memory or on-chip memory technology 
* Limited number of pipelines, small evaluation scope
* Justifcation for mobile platform