# PLOCTree
A Fast, High-Quality Hardware BVH Builder

---

## Background
<!--
Let's start with some basic concept
-->

---

### Bounding Volume Hierarchy (BVH)
<!--
In case you  ̶d̶o̶n̶'̶t̶ ̶k̶n̶o̶w̶ ̶a̶l̶r̶e̶a̶d̶y̶ are stupid, BVHs are kind of needed for raytracing...
... and this is relevant
... because raytracing is f***ing cool
-->

---

![](https://i.imgur.com/dnjAbHX.png =700x)
<!-- 
So you use volumes like Axis Alligned Boxes to bind geometry primitives
-->

---

![](https://i.imgur.com/uHKtdoK.png =700x)
<!-- 
... then you bind the boxes with other boxes and you get a hierarchy 
-->

---

![](https://i.imgur.com/tasDKhh.png =700x)

<!-- 
... which is actually a tree
-->

---

### BVH construction
<!-- it better be fast -->

---

#### BVH construction approaches:

1. **insertion** (incremental)
2. **subdivison** (top-down)
3. **agglomeration** (bottom-up)

---

![](https://i.imgur.com/1FUULyG.png =700x)

<!-- example of insertion, TODO: understand better how insertion-based solutions work, name some of such techiniques -->

---

### Surface Area Heuristic (SAH)

<!-- is this really needed here? -->

---

### Morton order

---

![](https://upload.wikimedia.org/wikipedia/commons/c/cd/Four-level_Z.svg)

<!--
morton order maps two dimentions into a single dimention (array) while preserving approximate distancing
-->

---

### Parallel Locality Ordered Clustering (PLOC)

---

PLOC is a massively **parallel**, **agglomerative** **BVH construction algorithm**

It orders clusters in **Morton order** to allow for efficient neighboring distance computation

---

#### The PLOC algorithm steps

---

1. Assign each AABB a morton code
2. Input AABB:s sorted in morton code order
3. Apply repeated iterations over array - PLOC sweeps

---

![](https://i.imgur.com/HZ4vajy.png =600x)
<!-- Illustration of the nearest neighbor search for r ¼ 2. Two clusters (red triangles) search for their nearest neighbors (blue triangles) in the neighborhood (red curve). Notice how the algorithm adapts to the den- sity of clusters in the neighborhood. -->


---

#### PLOC sweeps

<!--
1. NN-search - for every AABB - compute a distance to all AABB:s in window radius R. Select AABB with lowest distance as NN
2. Merging: Find pairs of AABB that are mutual nearest neighbors -merge each such pair into a BVH inner node. Place AABB of newly created node in one of the original array positions, and leave the other empty.
	AABB:s that do not have mutual NN:s are left unchanged.
3. Remove empty elements in the array
-->

---


![](https://i.imgur.com/TSEUX7o.png)

![](https://i.imgur.com/8rgJUZP.png)

---

![](https://i.imgur.com/hhuzRBo.png)

![](https://i.imgur.com/8rgJUZP.png)

---

![](https://i.imgur.com/GE9nVsl.png)

![](https://i.imgur.com/8rgJUZP.png)

---

![](https://i.imgur.com/YV5IpFE.png)

![](https://i.imgur.com/8rgJUZP.png)


---

## PLOCTree

<!-- How solution works (original contributions) -->

---

### Streaming algorithm

<!-- maybe move in backgound? -->

[wikipedia](https://en.wikipedia.org/wiki/Streaming_algorithm#Data_stream_model)

<!-- 
Lots of kernel launches - memory intensive - streaming behavoir
Manipulating data
-->


---

### PLOC Hardware implementation


---

#### Basic implementation
![](https://i.imgur.com/9MqpLKh.png)

* Lots of data streaming innitially
* Launching Kernel multiple times
<!--  -->

---

#### Optimize for hardware implementation

* Keep execution on chip
* Fuse loops
* Exploit moving in a radius around index

---

#### Resource, precision and adaptive collapsing
* Several resources - balance load
* Save some silicon - use reduced precision
<!-- Regions in BHV outside range of half precision clamped to max float -->
<!-- Cancellations when subtracting bounds along width -->
<!-- Solve with initially high precision and then lower -->
* Merging leafs - memory reagions might be spread out - maybe skip?


---


---

#### Hardware implementation

---

![](https://i.imgur.com/XmzWalB.png)

---

#### System architechture

![](https://i.imgur.com/xC0FU7w.png)

---

#### Evaluation

* RTL level in SystemVerilog
* 28nm, 1Ghz
<!-- power analysis based on switching activity, revord build times, mem traffic, build energy-->
* Various scenes
<!-- Fifteen test scenes, verify from RTL as hexdump and render using it-->
* Performance metrics
<!-- SAH cost of output trees -->
* GPU PLOC and LBVH on 1080 Ti
<!-- memory traffic extracted -->

## Results

---

<!-- 1-3W mobile gpu, nothing left for rendering -->
<!-- Run at full power on desktop -->
  
* PLOCTree consumes 1.4–1.9W 
* 7% slower than LBVH
* More power/energy consumtion than MergeTree
* 5 x faster with 3 x less bandwith usage, 5 x less silicon than "binned SAH builder"
<!-- -->
* 4 x faster and 8 x less bandwidth than GPU PLOC

---

![](https://i.imgur.com/3ozffhT.png)


<!-- -->

---

## Shortcomings



* Shaky performance metrics, not accurate
* Not acounting for newer memory or on-chip memory technology
<!-- might be obsolete -->
* Large leaf collapsing
<!-- different large areas, cant collapse efficiently, gain increase in performance -->
* Fixed radius and Morton code bitwidth
<!-- Too inprecise for large scenes -->
<!-- increasing radius would require alot of duplication on the hardware -->

___

![](https://i.imgur.com/AVXgN9x.png)
___

* Limited number of pipelines, small evaluation scope

* Usage beyond raytracing

* Justifcation for mobile platform