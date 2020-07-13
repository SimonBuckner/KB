# Graphviz - Graph Visualization Software

From their [website](https://graphviz.org/)

Graphviz is open source graph visualization software. Graph visualization is a way of representing structural information as diagrams of abstract graphs and networks. It has important applications in networking, bioinformatics, software engineering, database and web design, machine learning, and in visual interfaces for other technical domains.

## Overriding Defaults

Set the default node shape to a rectangle. [Full list](https://graphviz.org/doc/info/shapes.html)

```graphviz
graph example {
    node [shape=box]
    A
    B
    A -- B
}
```

Make all nodes the same fixed sized regardless of contents.

```graphviz
graph example {
    node [shape=box; fixedsize=true]
    A
    B
    A -- B
}
```

Or for a custom fixed size.

```graphviz
graph example {
    node [shape=box; size=1.5]
    A
    B
    A -- B
}
```

Change graph direction from **top to bottom** to **left to right**.

```graphviz
graph example {
    rankdir=LR
    A
    B
    A -- B
}
```
