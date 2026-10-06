# Dijkstra on Fire

Vertices are flammable, edges are gunpowder trails. The source is lit at time 0, and fire
travels along every trail at the same speed, so the moment a vertex catches fire is its
distance from the source.

- **Step / Play**: the slow way. *Step* burns every lit trail forward by the *step length*;
  *Play* burns continuously at the chosen rate (length units per second).
- **Next ignition**: Dijkstra. Jump straight to the next vertex to catch fire (extract-min),
  then light its fuses (relax), which may move neighbours' scheduled times earlier (decrease-key).
- The two can be mixed freely. The counters show steps vs. ignition jumps.

Keys: `→` step, `Space` play/pause, `N` next ignition, `R` reset.
A specific example can be linked as `index.html#clrs.json`.

## Running

It fetches `examples/*.json`, so serve the folder over http rather than opening the file:

    cd dijkstra-fire && python3 -m http.server 8000   # then open http://localhost:8000

No external dependencies; works offline.

## Graph format

Add a file to `examples/` and list it in `examples/index.json`
(or paste it into **Edit / load JSON…** on the page).

```json
{
  "name": "Triangle",
  "description": "Shown under the graph.",
  "directed": false,
  "source": "s",
  "step": 1,
  "nodes": [
    { "id": "s", "x": 0,   "y": 200 },
    { "id": "a", "x": 250, "y": 0 },
    { "id": "t", "x": 500, "y": 200 }
  ],
  "edges": [
    { "from": "s", "to": "a", "length": 3 },
    { "from": "a", "to": "t", "length": 2 },
    { "from": "s", "to": "t", "length": 7 }
  ]
}
```

- Required: `nodes`, `edges`. Optional: `name`, `description`, `directed` (default `false`),
  `source` (default: first node), `step` (default step length), `rate` (default play rate,
  length units per second). Without `step`/`rate`, defaults are picked from the edge lengths
  (step ≈ half the shortest edge; rate so the whole graph burns out in about 8 seconds).
- Shorthands: a node can be just `"s"`; an edge can be `["s", "a", 3]`.
- If an edge omits its length (`["s", "a"]`), the length is the distance between the endpoints'
  coordinates, rounded to an integer, so the picture is to scale. `examples/city.json` does this.
- `x`, `y` are in any units (y points down); the drawing is rescaled to fit. If any node lacks
  coordinates, all nodes are placed on a circle (and every edge then needs a length).
- Lengths must be ≥ 0 (negative lengths are rejected with an explanation). Self-loops aren't allowed.
- In directed graphs, antiparallel edges (u→v and v→u) are drawn curved automatically. An edge
  may set `"curve": 0.2` (or negative) to bend it manually, e.g. for parallel edges.
