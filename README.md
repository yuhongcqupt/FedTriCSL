The code for the manuscript entitled “FedTriCSL: Federated Causal Structure Learning via Support–Uncertainty Guided Three-Way Decisions”, submitted to Information Sciences, will be publicly released upon acceptance.

## Environment

- Python 3.8 or later (Python 3.10 or 3.11 recommended)
- Windows, Linux, or macOS

The required CPU dependencies are listed in `requirements.txt`. PyTorch is not
required for the included example.

## Install and Run

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
python example_minimal.py
```

Optional arguments:

```bash
python example_minimal.py \
  --samples 400 \
  --max-cond-set 3 \
  --seed 2026 \
  --output results/my_result.csv
```

Create the output directory before using a nested output path such as
`results/my_result.csv`.

## Input Settings

The included example generates synthetic input data, so no input file is
required.

For your own data, import `ThrFedSUB` in Python and pass one item per client.
If all clients observe the same variables, each item is a two-dimensional
NumPy array with shape `(number_of_samples, number_of_variables)`:

```python
from thr_fedsub import ThrFedSUB, ThrFedSUBConfig

clients = [client_1_array, client_2_array, client_3_array]
result = ThrFedSUB(ThrFedSUBConfig(max_cond_set=2)).fit(clients)
```

For clients with different observed variables, use a `data` and `mask`
dictionary for each client:

```python
clients = [
    {"data": client_1_array, "mask": [True, True, False, True]},
    {"data": client_2_array, "mask": [True, False, True, True]},
]
result = ThrFedSUB(ThrFedSUBConfig(max_cond_set=2)).fit(clients)
```

`mask` describes the global variables observed by that client. The columns in
`data` must follow the left-to-right order of the `True` entries in `mask`.
Inputs must not contain NaN or infinite values.

## Output Settings

By default, the example writes `example_output.csv` in the current directory.
Change the output file with `--output`:

```bash
python example_minimal.py --output my_result.csv
```

The CSV file contains one result row. Matrices, edge lists, per-edge decisions,
and the uncertainty trace are stored as compact JSON strings inside their CSV
cells. Its columns are:

- `skeleton_adjacency`: learned undirected skeleton matrix;
- `cpdag_adjacency`: learned CPDAG matrix;
- `directed_edges` and `undirected_edges`;
- `accepted_count`, `deferred_count`, and `rejected_count`;
- `edge_decisions`: per-edge three-way decisions;
- `uncertainty_trace`.

When using the Python API directly, the same results are available from
`result.skeleton_adjacency()`, `result.cpdag_adjacency()`,
`result.edge_decisions`, and the other fields of `result`.

