# PageRank with the Power Method

A from-scratch implementation of Google's PageRank algorithm in Python/NumPy, built as a homework for the course **Computational Linear Algebra for Large Scale Problems** (Politecnico di Torino, MSc, academic year 2025/2026).

The project ranks web pages by computing the dominant eigenvector of the "Google matrix"

```
M = (1 - m) A + m S
```

where `A` is the column-stochastic link matrix of the web, `S` is the matrix with all entries `1/n`, and `m = 0.15` is the damping parameter.

## What's inside

| File | Description |
| --- | --- |
| `PageRank_Implementation.ipynb` | The algorithm: sparse (COO) link-matrix construction, the power method, handling of dangling nodes, and experiments on small graphs, a real web crawl and a large synthetic web. |
| `PageRank_Exercises.ipynb` | Worked solutions to the theory and coding exercises (column-stochastic matrices, dimension of the eigenspace `V1(A)`, convergence rate, choice of `m`, ...). |
| `PageRank_Report.pdf` | Written report (21 pages): algorithm, computational cost, results and discussion. |
| `PageRank_Slides.pdf` | Presentation slides summarising the work. |
| `hollins.dat` | Web crawl of the hollins.edu domain: 6,012 pages and 23,875 links. |
| `graph1.png`, `graph2.png`, `graph_full.png` | Diagrams of the small example webs used in the notebooks. |

## How it works

- **Sparse storage.** The link matrix is never built densely for the large cases. Links are stored in coordinate (COO) form as two index arrays plus each page's out-degree.
- **Power method.** Each iteration computes `x ← (1 - m) A x + m/n` using `np.add.at` on the link arrays, so one step costs O(number of links) instead of O(n²).
- **Dangling nodes.** Pages with no outgoing links would break column-stochasticity; their score is redistributed uniformly over all pages at every step.
- **Stopping rule.** Iteration stops when the 1-norm of the change between two successive vectors drops below `1e-8`.

## Results

| Web | Pages | Links | Power method | `np.linalg.eig` |
| --- | --- | --- | --- | --- |
| Example graph 1 | 4 | 8 | converged in 23 iterations | same eigenvector (error ≈ 1.7e-9) |
| Example graph 2 | 5 | 6 | converged in 1 iteration | same eigenvector (error ≈ 1.8e-15) |
| Hollins crawl | 6,012 | 23,875 | 83 iterations, ≈ 0.08 s | ≈ 83 s (error ≈ 2.7e-9) |
| Random synthetic web | 900,000 | 3,600,000 | 24 iterations, ≈ 3.3 s | not feasible (dense matrix) |

On the Hollins crawl the sparse power method gives the same ranking as the dense eigen-solver roughly a thousand times faster. Timings are from the author's laptop and will vary by machine.

## Running it

```bash
pip install -r requirements.txt
jupyter notebook
```

Open `PageRank_Implementation.ipynb` and run the cells in order, from the folder that contains `hollins.dat`.

The last section of the notebook generates `large_web_graph.dat` (a random web of 900,000 pages, roughly 90 MB). It is created locally when you run the cell and is deliberately not stored in this repository.

## References

- K. Bryan and T. Leise, *The $25,000,000,000 Eigenvector: The Linear Algebra Behind Google*, SIAM Review 48(3), 2006. The exercises solved in `PageRank_Exercises.ipynb` come from this paper.
- L. Page, S. Brin, R. Motwani and T. Winograd, *The PageRank Citation Ranking: Bringing Order to the Web*, 1998.

## Author

Pietro Fabi
