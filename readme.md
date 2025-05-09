# Contraction‑Hierarchy Demo

An interactive, in‑browser playground for **Contraction Hierarchies (CH)**—the speed‑up technique behind lightning‑fast route planners such as Google Maps, OSRM, and GraphHopper.

👉 **Live demo:** [https://scalemailted.github.io/contraction-hierarchy-demo/](https://scalemailted.github.io/contraction-hierarchy-demo/)

---

## 1  What are Contraction Hierarchies?

A vanilla Dijkstra or A\* search visits *lots* of nodes. CH accelerates queries in two phases:

1. **Pre‑processing (once):**
     \* Contract vertices one by one (lowest “importance” first).
     \* While contracting a vertex *v*, insert *shortcuts* between its higher‑level neighbours if the path `neighbour → v → neighbour` is the unique shortest path.
     \* Record an *order* (a.k.a. level) for every vertex.

2. **Query time (milliseconds):**
     \* Run a bidirectional Dijkstra that is *upward‑only*—edges are followed only toward higher‑level vertices.
     \* The forward and reverse searches meet quickly; the best meeting node yields the optimal path length.
     \* Finally **unpack** any shortcuts encountered to recover a path that uses only original edges.

Because heavy lifting happens up front, online queries touch a tiny fraction of the graph—often < 1 ‰ of the nodes in a continental‑scale network.

---

## 2  About This Demo

This project condenses the algorithm into \~300 lines of vanilla JS **(no frameworks, no build‑step)** and renders onto an HTML5 canvas.

### Key Features

| Feature                        | Description                                                                           |
| ------------------------------ | ------------------------------------------------------------------------------------- |
| **Point‑&‑click graph editor** | Click to add nodes, drag between nodes to create weighted edges (Euclidean distance). |
| **One‑button pre‑processing**  | Computes the contraction hierarchy & inserts shortcuts.                               |
| **Bidirectional CH query**     | Enter start/end IDs and get the fastest route—**unpacked** to the original edges.     |
| **Visualisation**              | • Gray lines = original edges  • Light‑gray = shortcuts  • Red = returned path.       |

### Files

| File                              | Purpose                                                  |
| --------------------------------- | -------------------------------------------------------- |
| `contraction-hierarchy-demo.html` | Self‑contained demo (UI, algorithm, and canvas drawing). |
| `README.md`                       | *You’re reading it.*                                     |

---

## 3  Using the Demo

1. **Open the live page** or load `contraction-hierarchy-demo.html` locally.
2. **Build a network**

   * *Click* the canvas to drop nodes.
   * *Drag* from one node to another to add an edge.
3. **Pre‑process** – press **Pre‑process Graph**.
4. **Query**

   * Enter the numeric IDs (shown beside each node) in the *Start ID* and *End ID* fields.
   * Click **Find Path**. The shortest route appears in red along with distance and node count.

> **Tip:** Shortcuts speed up the search but are automatically unpacked, so the red route only follows original edges—exactly what you’d drive in real life.

---

## 4  Algorithm Details (for the curious)

* **Node ordering heuristic:** The demo contracts the current lowest‑degree vertex—simple yet effective for small graphs. Production engines use more elaborate cost functions (edge difference, search space size, hierarchical depth…).
* **Witness search cut‑off:** While contracting, a bounded Dijkstra (max cost = prospective shortcut cost) checks if an alternative path exists that avoids the vertex.
* **Shortcut structure:** Each shortcut stores a single *via* vertex so it can be recursively unpacked.
* **Complexities:**  Pre‑processing ≈ O\*(n log n) with a good order; queries are \~O(k log n) where *k* is meeting‑frontier size (often ≪ √n).

---

## 5  Next Steps & Ideas

* **Import GeoJSON / OSM extracts** to play with real road networks.
* **Experiment with ordering heuristics** (edge diff, contraction levels, randomised tie‑breaks).
* **Toggle shortcut visibility** or show contraction levels with colour.
* **Export the graph** to JSON for downstream algorithms.

Pull requests and feedback welcome—have fun exploring fast routing!

