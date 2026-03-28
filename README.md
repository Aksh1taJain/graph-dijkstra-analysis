# Assignment question ---->
# graph-dijkstra-analysis
# Analysis of `simple-dijkstra` (C) — Dijkstra’s Shortest Path

Source analyzed: https://github.com/nvurgaft/simple-dijkstra

## 1) What this project does (in one paragraph)
This is a minimal C implementation of Dijkstra’s shortest-path algorithm over a directed weighted graph. It reads a simple “config” language to add vertices/edges, remove edges, request a shortest-path computation from a source, and then exits. Internally it uses a custom doubly-linked list utility for generic lists, and a straightforward Dijkstra loop (array/linear scans rather than a fancy heap), which is fine for small graphs. (Ref: project README and file inventory). 

## 2) Repository layout (from upstream README)
- `dijkstra.c` — contains `main`, graph data, and the Dijkstra algorithm.
- `dijkstra.h` — headers for the Dijkstra module.
- `dbllist.c/.h` — a generic doubly-linked list implementation used by the app.
- `test_config` — sample configuration file / format description.
(Ref: repo landing page & README.) 

## 3) Config file grammar (how the program is driven)
From the README:
- `0 <id>` — define vertex with numeric id (0..n).
- `1 <src> <dest> <weight>` — add directed edge with weight.
- `2 <src> <dest>` — remove edge.
- `3 <src>` — run Dijkstra from `<src>`.
- `4` — end: free resources and exit.
(Ref: repo README section showing commands.) 

## 4) Likely data structures
Because the repo uses `dbllist.c/.h`, the code likely represents:
- **Vertices**: integers (ids) plus arrays for `dist[]`, `prev[]`, and `visited[]`.
- **Edges**: adjacency list per vertex (dest, weight), probably stored via the doubly-linked list or simple arrays.
- **Graph**: count of vertices + adjacency structure.

> Note: Exact struct names may differ, but these are the standard fields for a Dijkstra implementation in C.

## 5) Function-by-function walkthrough (mapped to behavior)
The exact function names can vary; below is a mapping of responsibilities you’ll find in `dijkstra.c/.h` and `dbllist.c/.h`. Use `ctags -x --c-kinds=f *.c` to list the actual names in your clone and cross-check.

### In `dijkstra.c`
- **`main(int argc, char** argv)`**  
  Parses the config file, dispatches commands (`0/1/2/3/4`), builds the graph, runs Dijkstra on command `3`, prints results, and on `4` frees memory and exits.

- **Graph construction helpers** (names vary, e.g., `add_vertex`, `add_edge`, `remove_edge`)  
  Maintain the adjacency structure:
  - `add_vertex(id)`: ensure vertex arrays/lists are sized to include `id`.
  - `add_edge(src, dest, w)`: append/update (dest, weight) in `src`’s adjacency.
  - `remove_edge(src, dest)`: delete matching adjacency entry.

- **`dijkstra(Graph* g, int src)`**  
  Core algorithm:
  1. Initialize `dist[v]=INF`, `prev[v]=-1`, `visited[v]=false` for all v; set `dist[src]=0`.
  2. Repeat |V| times:
     - pick the unvisited vertex `u` with the smallest `dist[u]` (linear scan).
     - mark `u` visited.
     - relax all outgoing edges `(u -> v, w)`: if `dist[u]+w < dist[v]`, update `dist[v]` and `prev[v]=u`.
  3. Optionally print distances and/or reconstruct a path with `prev[]`.

- **Printing / path reconstruction** (e.g., `print_distances`, `print_path(prev, target)`):  
  Convert `prev[]` chain into a human-readable path and display distances.

- **Cleanup** (e.g., `free_graph`)  
  Free adjacency lists and arrays.

### In `dbllist.c/.h` (generic utility)
Expect typical operations such as:
- `dll_init`, `dll_push_back`, `dll_remove`, `dll_find`, `dll_free`, and a `node` struct with `prev/next/value`.
These are used to store adjacency or miscellaneous collections.

> If you want verbatim function names: run `ctags -x --c-kinds=f *.c` in the project root after cloning; copy the symbol list here and replace the “names vary” items with the actual names.

## 6) Algorithmic complexity
- With linear search for the next minimum: **O(V^2 + E)** (effectively O(V^2) for dense/small graphs).
- With adjacency lists, relaxations are O(E). No binary/Fibonacci heap used (fits the README’s “basic” claim).

## 7) How to build and run locally
```bash
git clone https://github.com/nvurgaft/simple-dijkstra.git
cd simple-dijkstra
# basic build (likely)
cc -O2 dijkstra.c dbllist.c -o dijkstra
# run with a config file (adjust if program expects stdin):
./dijkstra < test_config
