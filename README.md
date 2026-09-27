# Stagecoach Dynamic Programming Solver

Made by [Visqy](https://github.com/Visqy) and [AkmalMakarim](https://github.com/AkmalMakarim).

An interactive Python application for solving and visualizing the **Stagecoach Problem** using dynamic programming.

The application models a layered graph in which decisions are made stage by stage. Users can define the graph, choose whether to minimize or maximize the objective, inspect the backward dynamic programming process, reconstruct all optimal paths, and visualize the resulting graph through a Streamlit interface.

## Overview

The Stagecoach Problem is a multistage optimization problem in which a path must be selected through a sequence of stages while minimizing or maximizing an accumulated objective.

This project turns that formulation into an interactive solver. Instead of working only with a static example, users can provide their own staged graph and edge weights in JSON format, run the solver, inspect the dynamic programming values computed at each stage, and compare the resulting optimal paths visually.

The project separates the interface from the core solver logic: `app.py` provides the Streamlit GUI, while `stagecoach.py` handles the dynamic programming computation, optimal-path reconstruction, and graph visualization.

## Features

- Define staged nodes (`layers`) and weighted transitions (`edges`) using JSON.
- Choose the optimization objective:
  - `min` for minimum cost/value.
  - `max` for maximum value.
- Choose the aggregation operation:
  - `+` for additive objectives.
  - `*` for multiplicative objectives.
- Inspect the **backward dynamic programming computation** stage by stage.
- Reconstruct **all optimal paths**, not only a single solution.
- Visualize the stagecoach graph and download the generated graph as a PNG image.
- Validate graph structure before solving.

## Project Structure

```text
.
├── app.py            # Streamlit user interface
├── stagecoach.py     # DP solver, optimal-path reconstruction, and graph plotting
├── requirements.txt  # Python dependencies
└── README.md
```

## Requirements

- Python **3.9+** (Python 3.10 or 3.11 recommended)
- `pip`

## Installation

Clone the repository and move into the project directory:

```bash
git clone https://github.com/Visqy/stagecoach-solver-gui.git
cd stagecoach-solver-gui
```

Create a virtual environment:

```bash
python -m venv .venv
```

Activate it on Windows:

```bash
.venv\Scripts\activate
```

Or on macOS/Linux:

```bash
source .venv/bin/activate
```

Install the required dependencies:

```bash
pip install -r requirements.txt
```

## Running the Application

Start the Streamlit application from the project directory:

```bash
streamlit run app.py
```

If the `streamlit` command is not recognized, use:

```bash
python -m streamlit run app.py
```

Streamlit will normally open the application in your browser at:

```text
http://localhost:8501
```

## Usage

1. Open the application.
2. Define the **Layers** and **Edges** in JSON format, or load the provided example.
3. Select the **Start** and **Goal** nodes.
4. Choose the optimization mode (`min` or `max`).
5. Choose the aggregation operation (`+` or `*`).
6. Run the solver.
7. Inspect the results:
   - **Result** — optimal value, one selected optimal path, and the complete set of optimal paths.
   - **Process** — the dynamic programming computation for each stage.
   - **Visualization** — the staged graph and its optimal solution paths.
   - **About** — a summary of available options and valid input examples.

## Input Format

### Layers

`layers` is an ordered list of lists representing the graph from the first stage to the final stage.

```json
[
  ["S"],
  ["A", "B"],
  ["C", "D"],
  ["T"]
]
```

The selected **Start** node must belong to the first stage, while the **Goal** node must belong to the final stage.

### Edges

`edges` uses a nested dictionary structure:

```text
source -> {target: weight}
```

Example:

```json
{
  "S": {"A": 2, "B": 5},
  "A": {"C": 4, "D": 1},
  "B": {"C": 2},
  "C": {"T": 3},
  "D": {"T": 2}
}
```

Each edge must connect a node in stage `i` to a node in stage `i + 1`. Edges that skip stages are rejected by validation.

## Complete Configuration Example

```json
{
  "layers": [["S"], ["A", "B"], ["C", "D"], ["T"]],
  "edges": {
    "S": {"A": 2, "B": 5},
    "A": {"C": 4, "D": 1},
    "B": {"C": 2},
    "C": {"T": 3},
    "D": {"T": 2}
  },
  "start": "S",
  "goal": "T",
  "opt_mode": "min",
  "combine_op": "+"
}
```

## Solver Behavior and Constraints

- Edges must connect consecutive stages only.
- Nodes must not appear more than once in `layers`.
- For `opt_mode="min"`, the solver uses `+∞` as the initial comparison value.
- For `opt_mode="max"`, the solver uses `-∞` as the initial comparison value.
- For additive objectives (`combine_op="+"`), the terminal value at the goal node is `0.0`.
- For multiplicative objectives (`combine_op="*"`), the terminal value at the goal node is `1.0`.
- Graph visualization uses Matplotlib. When running in a server environment without a display, Streamlit handles the headless backend.

## Troubleshooting

### `streamlit: command not found` or `streamlit is not recognized`

Make sure the virtual environment is active, or run:

```bash
python -m streamlit run app.py
```

### `Failed to import stagecoach.py`

Make sure `stagecoach.py` is located in the same project directory as `app.py`.

### Validation reports that an edge skips a stage

Check that every edge connects a node in stage `i` directly to a node in stage `i + 1`.

### The graph is empty or no path is shown

Verify the JSON input and graph connectivity. A practical way to debug the input is to start from the provided example and modify it incrementally.

## Purpose

This project demonstrates how a multistage dynamic programming formulation can be translated into an interactive software tool, combining algorithmic computation, input validation, optimal-path reconstruction, and graph-based visualization in a single application.
