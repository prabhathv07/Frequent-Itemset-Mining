# Frequent Itemset Mining

![Python](https://img.shields.io/badge/Python-3.12-blue?logo=python&logoColor=white)
![mlxtend](https://img.shields.io/badge/mlxtend-0.23-orange)
![Pandas](https://img.shields.io/badge/Pandas-2.x-150458?logo=pandas&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green)

A comparison of three market basket analysis algorithms — **Brute Force**, **Apriori**, and **FP-Growth** — across five retail transaction datasets.

---

## What This Does

Given a dataset of customer transactions, the program finds all combinations of items that appear together frequently (above a configurable support threshold), then generates association rules (e.g., *"customers who buy headphones also buy a case 70% of the time"*). Three separate algorithms produce the same itemsets so their speed and correctness can be compared side by side.

---

## Why This Matters

Retailers need to understand which products sell together to drive recommendations, bundle pricing, and shelf placement. Traditional brute-force search is correct but computationally expensive. Apriori prunes the search space using the downward closure property. FP-Growth avoids candidate generation entirely via a compact tree structure. This project quantifies the trade-offs across all three on real retail datasets.

---

## Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                     Input Layer                              │
│   amazon.csv  bestbuy.csv  kroger.csv  target.csv  walmart.csv│
└───────────────────────┬──────────────────────────────────────┘
                        │
                        ▼
              ┌─────────────────┐
              │   DataManager   │  load & verify datasets
              └────────┬────────┘
                       │  transactions[]
          ┌────────────┼─────────────┐
          ▼            ▼             ▼
  ┌──────────────┐ ┌────────┐ ┌──────────┐
  │ BruteForce   │ │Apriori │ │ FP-Growth│
  │  (custom)    │ │mlxtend │ │ mlxtend  │
  └──────┬───────┘ └───┬────┘ └────┬─────┘
         └─────────────┴───────────┘
                        │
                        ▼
              ┌──────────────────┐
              │ ResultDisplayer  │  side-by-side benchmark table
              └──────────────────┘
```

---

## Datasets

Five retail transaction datasets included in the repository:

| Dataset   | Store       | Transactions |
|-----------|-------------|-------------|
| amazon    | Amazon      | 20          |
| bestbuy   | Best Buy    | 20          |
| kroger    | Kroger      | 20          |
| target    | Target      | 20          |
| walmart   | Walmart     | 20          |

Each CSV has a single `items` column — a comma-separated list of product names per transaction row.

---

## Algorithms

### Brute Force
Generates every possible k-itemset (k = 1, 2, 3, …) using `itertools.combinations` and computes support by scanning all transactions. Guaranteed correct; exponential in the number of unique items.

### Apriori (mlxtend)
Applies the downward closure property: if an itemset is infrequent, all its supersets are also infrequent. Prunes candidates at each level, dramatically reducing the search space on sparse data.

### FP-Growth (mlxtend)
Compresses the transaction database into an FP-tree (Frequent Pattern tree). Mines patterns by traversing conditional trees for each item — no candidate generation phase at all.

---

## Sample Results

Run with Amazon dataset, `min_support = 0.2`, `min_confidence = 0.5`:

| Algorithm   | Time (s) | Frequent Itemsets | Association Rules |
|-------------|----------|-------------------|-------------------|
| Brute Force | 0.0019   | 6                 | 0                 |
| Apriori     | 0.0035   | 6                 | 0                 |
| FP-Growth   | 0.0034   | 6                 | 0                 |

All three algorithms produce identical itemset counts — correctness is verified by agreement. FP-Growth and Apriori carry framework overhead on tiny datasets; the gap widens significantly with larger transaction volumes.

---

## Tech Stack

| Component  | Technology           |
|------------|----------------------|
| Language   | Python 3.12          |
| Mining     | mlxtend 0.23         |
| Data       | pandas 2.x           |
| Notebook   | Jupyter / JupyterLab |

---

## Repository Structure

```
Frequent-Itemset-Mining/
├── frequent_itemset_mining.ipynb   # Notebook — all algorithms + demo run
├── frequent_itemset_mining.py      # Standalone interactive CLI
├── amazon.csv
├── bestbuy.csv
├── kroger.csv
├── target.csv
├── walmart.csv
├── LICENSE
└── README.md
```

---

## How to Run

### Jupyter Notebook (recommended)

```bash
# Install dependencies
pip install pandas mlxtend jupyter

# Launch notebook
jupyter notebook frequent_itemset_mining.ipynb
```

Open the notebook and run all cells. Edit the three variables in the **Example Run** section to change the dataset, support threshold, or confidence threshold.

### Interactive CLI

```bash
python frequent_itemset_mining.py
```

Prompts you to select a dataset (1–5) and enter support/confidence values at runtime, then prints results for all three algorithms with a comparison table.

---

## Key Observations

- **Brute Force** is the correctness baseline — exhaustive and unoptimized.
- **Apriori** and **FP-Growth** match brute force on itemset counts, confirming their correctness.
- On small datasets (20 transactions) all three finish in milliseconds; framework overhead makes library algorithms slower on tiny inputs.
- As dataset size grows, FP-Growth consistently outperforms Apriori due to eliminating the candidate-generation phase.
- With `min_support = 0.2` on the Amazon dataset, no rules meet `min_confidence = 0.5` — lowering either threshold reveals richer rule sets.

---

## Future Improvements

- Visualize support vs. itemset count curves for each algorithm
- Test on larger public datasets (e.g., UCI Online Retail)
- Implement ECLAT as a fourth algorithm for set-intersection-based comparison
- Parallelize the brute-force scan across CPU cores for fair large-scale benchmarking
- Export discovered rules to a structured CSV / HTML report

---

## Author

**Prabhath Vinay Vipparthi**  
[GitHub](https://github.com/prabhathv07) · [Email](mailto:prabhath867@gmail.com)
