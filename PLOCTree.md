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
In case you don't know already BVHs are kind of needed for raytracing...
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


<!-- example of insertion -->

---

### Surface Area Heuristic (SAH)

---



---

### Parallel Locality Ordered Clustering (PLOC)

---

PLOC is a massively **parallel**, **agglomerative** **BVH construction algorithm**

It orders clusters in **Morton order** to allow for efficient neighboring distance computation

---

#### The PLOC algorithm steps:
1.
2.
3.

<!--
bla bla
-->

---

### Morton order

---

![](https://upload.wikimedia.org/wikipedia/commons/c/cd/Four-level_Z.svg)

<!--
morton order maps two dimentions into a single dimention (array) while preserving approximate distancing
-->

---

## PLOCTree

---

![](https://i.imgur.com/qlafjcL.png)

<!-- How solution works (original contributions) -->

---

### Streaming algorithm
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

![](https://i.imgur.com/qlafjcL.png)

----

#### System architechture

![](https://i.imgur.com/xC0FU7w.png)

----

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

* PLOCTree consumes 1.4–1.9W 
<!-- 1-3W mobile gpu, nothing left for rendering -->
<!-- Run at full power on desktop -->
* 7% slower than LBVH
* More power/energy consumtion than MergeTree
* 5 x faster with 3 x less bandwith usage, 5 x less silicon than "binned SAH builder"
<!-- -->
* 4 x faster and 8 x less bandwidth than GPU PLOC

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

____

![](https://i.imgur.com/AVXgN9x.png)
____

* Limited number of pipelines, small evaluation scope

* Usage beyond raytracing

* Justifcation for mobile platform