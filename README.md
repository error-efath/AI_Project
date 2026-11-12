# Comparative Analysis of BFS, Bidirectional BFS, and A* Search on the SNAP Facebook Graph

**Course:** ICE 4107 – Artificial Intelligence  
**Project type:** Search algorithm implementation and experimental comparison  
**Main file:** `AI_PROJECT.ipynb`

## 1. Project Overview

This project implements and compares four shortest-path search approaches on the **Stanford SNAP Facebook combined graph**:

1. **Breadth-First Search (BFS)**
2. **Bidirectional Breadth-First Search**
3. **A* Search with an ALT landmark heuristic (original implementation)**
4. **Improved A* Search with deeper-node tie-breaking**

The objective is to evaluate how these algorithms perform on the same unweighted, undirected social network. The comparison considers shortest-path correctness, number of expanded nodes, execution time, and memory usage.

The notebook downloads and loads the dataset, constructs an adjacency-list graph, runs experiments, validates returned paths, generates tables and visualizations, and packages the output files.

## 2. Dataset

The project uses the **Facebook Combined** dataset from the Stanford Large Network Dataset Collection (SNAP).

- **Dataset page:** https://snap.stanford.edu/data/ego-Facebook.html
- **Download file:** https://snap.stanford.edu/data/facebook_combined.txt.gz
- **Graph type:** Undirected and unweighted
- **Nodes:** 4,039
- **Edges:** 88,234

The notebook reads the edge list and builds an adjacency-list representation. It removes self-loops and duplicate edges while loading the graph.

> Dataset credit: J. McAuley and J. Leskovec, *Learning to Discover Social Circles in Ego Networks*, NIPS 2012. Dataset distributed by the Stanford SNAP project.

## 3. Algorithms

### 3.1 Breadth-First Search (BFS)

BFS explores the graph level by level, starting from the source node. It uses a FIFO queue and finds a shortest path in an unweighted graph.

- **Purpose:** Baseline shortest-path algorithm.
- **Data structure:** Queue.
- **Shortest-path guarantee:** Yes, for unweighted graphs.

### 3.2 Bidirectional BFS

Bidirectional BFS searches simultaneously from the source and target. It maintains two frontiers and stops after finding the minimum valid connection between the searches.

- **Purpose:** Reduce the search space by searching from both endpoints.
- **Data structures:** Two queues and two visited/parent structures.
- **Shortest-path guarantee:** Yes, for unweighted graphs, as implemented in the notebook.

### 3.3 A* with ALT Landmark Heuristic

A* prioritizes nodes using `f(n) = g(n) + h(n)`, where `g(n)` is the distance from the source and `h(n)` estimates the remaining distance to the target.

This project uses an ALT-style heuristic based on precomputed distances from selected landmarks. The heuristic is calculated using landmark distance differences and is admissible for the graph-distance problem.

- **Purpose:** Guide the search toward the target.
- **Data structure:** Priority queue (min-heap).
- **Additional cost:** Landmark-distance preprocessing and storage.

### 3.4 Improved A*

The improved version keeps the same ALT heuristic but changes how equal-priority nodes are selected:

- Heap entries use `(f, -g, counter, node)`, preferring a deeper node when `f` values are equal.
- The target's landmark distances are looked up once per query rather than repeatedly during heuristic evaluation.

These changes affect tie-breaking and implementation overhead, not the heuristic or the shortest-path objective.

## 4. Experimental Methodology

The notebook uses a reproducible experimental setup:

- Random seed: `42`
- Number of source-target pairs: `200`
- Timing repetitions per query: `5`
- Reported query time: median of the repeated runs
- BFS path length: reference value for checking the other algorithms
- Memory measurement: Python `tracemalloc`, measured separately from timing

For every query, the project records path length, expanded-node count, execution time, and correctness information. It also measures A* landmark preprocessing/storage separately from per-query memory.

The graph is shared by all algorithms and is not included in the per-query peak-memory measurement.

## 5. Correctness Validation

Returned paths are checked to ensure that:

1. The path begins at the requested source.
2. The path ends at the requested target.
3. Every consecutive pair of nodes is connected by an edge.
4. The path contains no invalid or unexpected repeated nodes.

The path lengths returned by Bidirectional BFS and both A* implementations are compared with BFS, which serves as the baseline on this unweighted graph.

## 6. Visualizations and Results

The notebook generates CSV result tables and figures, including:

- Graph topology statistics
- Sample shortest-path results
- Aggregate algorithm comparison
- Correctness validation
- Experimental configuration and theoretical properties
- Search effort grouped by shortest-path length
- A* heuristic-quality analysis
- Performance comparison charts
- Sample shortest-path subgraph
- Expanded-node search-process diagrams
- Zoomed-in search-process diagrams
- BFS search-tree visualization

The figures show how much of the graph each algorithm explores and how the search progresses from the source toward the target.

## 7. Technologies and Libraries

- **Python 3**
- **Pandas** – tabular experiment data and CSV export
- **NumPy** – numerical operations
- **Matplotlib** – charts and graph visualizations
- **NetworkX** – graph layout for visualization
- **Python standard library** – queues, heaps, randomization, timing, statistics, compression, and memory profiling

## 8. How to Run the Project

### Option A: Google Colab (recommended)

1. Download or clone this project repository.
2. Open `AI_PROJECT.ipynb` in [Google Colab](https://colab.research.google.com/).
3. Select **Runtime → Run all**.
4. Allow the notebook to download the SNAP dataset when it is not already available.
5. Wait for the experiments, tables, and visualizations to finish.
6. The notebook saves generated deliverables in `snap_facebook_results/` and creates a ZIP archive named `facebook_search_project_deliverables.zip`. In Google Colab, it attempts to download that archive automatically.

### Option B: Run locally

1. Install Python 3.10 or later.
2. Install the required packages:

   ```bash
   pip install pandas numpy matplotlib networkx
   ```

3. Open the notebook using Jupyter:

   ```bash
   pip install notebook
   jupyter notebook AI_PROJECT.ipynb
   ```

4. Run all cells in order.

The dataset is downloaded automatically using `wget` if it is not already present. If your local environment does not have `wget`, download `facebook_combined.txt.gz` from the SNAP link above and place it in the notebook's working directory before running the notebook. The notebook also supports an uncompressed `facebook_combined.txt` file.

## 9. Output Structure

After a successful run, the notebook creates the following structure:

```text
project/
├── AI_PROJECT.ipynb
├── README.md
├── facebook_combined.txt.gz          # downloaded dataset (or .txt)
├── facebook_search_project_deliverables.zip
└── snap_facebook_results/
    ├── dataset_statistics.csv
    ├── raw_search_experiments.csv
    ├── sample_path_results.csv
    ├── algorithm_comparison.csv
    ├── correctness_validation.csv
    ├── experimental_configuration.csv
    ├── theoretical_properties.csv
    ├── effort_by_path_length.csv
    ├── heuristic_quality.csv
    ├── performance_comparison_charts.png
    ├── effort_vs_path_length.png
    ├── sample_path_visualization.png
    ├── explored_nodes_pair*.png
    ├── explored_zoom_pair*.png
    └── bfs_search_tree_pair1.png
```

The exact set of generated figures may depend on successful completion of the corresponding notebook cells.

## 10. Notes and Limitations

- The dataset is an unweighted graph, so BFS and Bidirectional BFS are appropriate shortest-path baselines.
- A* performance depends on the quality of its landmark heuristic. Landmark preprocessing introduces an additional one-time cost and memory requirement.
- The notebook uses a fixed random seed and fixed query sample to make the experiment reproducible.
- Query timings can vary with hardware, Python version, operating system, and system load. Compare algorithms in the same environment.
- Graph topology statistics that rely on sampled shortest paths are estimates based on the sample described in the notebook; they are not exhaustive all-pairs shortest-path measurements.

## 11. Project Goal

The project demonstrates the practical trade-offs between uninformed search and heuristic search on a real-world social network. It examines whether searching from both endpoints or using landmark-based A* can reduce the number of explored nodes and query cost while preserving shortest-path correctness.

---

**Course project:** ICE 4107 – Artificial Intelligence  
**Notebook:** `AI_PROJECT.ipynb`
