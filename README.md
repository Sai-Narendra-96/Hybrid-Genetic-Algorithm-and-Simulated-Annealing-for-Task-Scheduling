# Hybrid GA+SA for Edge–Fog–Cloud Task Scheduling

This repository contains the code, data, and document sources for our research on  
**Hybrid Genetic Algorithm + Simulated Annealing** (GA+SA) to schedule IoT tasks across  
three compute tiers: **Edge**, **Fog**, and **Cloud**.  

---

## 📄 Overview

Modern IoT applications generate thousands of tasks that can be run on:

- **Edge devices** (sensors, smartphones) — very low latency (<10 ms), limited CPU/memory/battery  
- **Fog nodes** (local gateways, micro data centers) — moderate latency (10–50 ms), moderate resources  
- **Cloud servers** (remote data centers) — high latency (50–200 ms), high compute capacity  

Assigning _N_ tasks to these 3 tiers yields \(3^N\) possible schedules (NP-Hard). Exact search is infeasible for large _N_. We propose a **two-stage heuristic**:

1. **Genetic Algorithm (GA)** — global exploration via population evolution (selection, crossover, mutation, elitism)  
2. **Simulated Annealing (SA)** — local refinement of each GA offspring, escaping local minima via Metropolis acceptance  

We evaluate our approach using the **2019 Google Cluster Workload Trace** dataset with 10,000 preprocessed tasks, characterized by CPU load (mean: 1.000733 units), data size (mean: 1.097850 MB), and deadlines (mean: 15.323545 s). We compare four methods—**Brute Force (batched)**, **GA-only**, **SA-only**, and **Hybrid GA+SA**—across six metrics:  
`Total Cost` (weighted sum of latency+energy), `Runtime`, `Average Latency`, `Total Energy`, `Deadline Violations`, and `Utilization Imbalance`.  

Our hybrid GA+SA method achieves a cost of 455,776.14 units in 1,746.51 seconds, outperforming GA-only (464,218.35 units) and SA-only (492,005.12 units), with strong convergence over 100 generations.

### What We’ve Done
This project has been enhanced with the following contributions:

- **Algorithm Design**: We designed and proposed a hybrid GA+SA algorithm that integrates GA’s global search with SA’s local refinement to efficiently schedule IoT tasks across edge, fog, and cloud tiers, optimizing latency, energy, and load balance.
- **Problem Formulation**: We formulated the task scheduling problem in edge-fog-cloud IoT environments, addressing the NP-Hard challenge of \(3^N\) possible schedules by incorporating task characteristics, compute tier properties, and a multi-objective cost function with latency and energy constraints.
- **Evaluation and Analysis**: We evaluated the hybrid GA+SA algorithm against brute force, GA-only, and SA-only methods using the 2019 Google Cluster Workload Trace dataset with 10,000 tasks, demonstrating a cost of 455,776.14 units, a runtime of 1,746.51 seconds, and strong convergence over 100 generations.

Additionally, we improved the accompanying research paper by adding citations to key concepts in the Introduction (e.g., IoT architecture, NP-Hardness, GA, SA), adjusting the font sizes in the literature comparison table for better readability (larger author names, default-sized headers), and restructuring the Introduction to clearly outline our contributions and paper organization.

---

## 🚀 Getting Started

1. **Clone the repo**  
   ```bash
   git clone https://github.com/your-username/hybrid-ga-sa-scheduling.git
   cd hybrid-ga-sa-scheduling
   ```

2. **Create a Python environment**  
   ```bash
   python3 -m venv venv
   source venv/bin/activate
   pip install -r requirements.txt
   ```
   _Requirements:_ `pandas`, `numpy`, `matplotlib`

3. **Run the Jupyter notebooks** in `notebooks/` for step-by-step experiments:  
   ```bash
   jupyter lab
   ```

4. **Compile the paper** (if you have LaTeX installed):  
   ```bash
   cd paper
   pdflatex main.tex
   ```

---

## 🔧 Definitions & Conventions

- **Task parameters**  
  - `w_i`: CPU load (abstract units)  
  - `d_i`: Data size (MB)  
  - `D_i`: Deadline (s)  

- **Tier characteristics**  
  - `C_τ`: compute capacity (units/s)  
  - `L_{src→τ}`: base network latency (s)  
  - `B_{src→τ}`: bandwidth (MB/s)  
  - `P_{idle,τ}`, `P_{peak,τ}`: idle/peak power (W)  

- **Latency model**  
 $$
\text{Latency}(t_i, \tau) = \frac{w_i}{C_\tau} + L_{src,\tau} + \frac{d_i}{B_{src,\tau}}
$$


- **Energy model**  
  Utilization $u_\tau = \frac{\sum w_i}{C_\tau}$.  
Energy $E_\tau(u) = P_{\text{idle},\tau} + (P_{\text{peak},\tau} - P_{\text{idle},\tau}) \cdot u_\tau$.

- **Objective**  
  Minimize $\alpha \sum \text{Latency} + \beta \sum E$, with trade-off weights $\alpha, \beta$. 

- **NP-Hardness**  
  The problem generalizes the Generalized Assignment Problem; exact search is \(\mathcal O(3^N)\).

- **Random seeds**  
  All experiments fix `random.seed(42)` and `np.random.seed(42)` for reproducibility.

- **Chromosome encoding**  
  A list of length `N`, each gene ∈ `{0,1,2}` mapping to `['edge','fog','cloud']`.

---

## 🧪 Experiments & Results

- **Dataset**: Preprocessed 2019 Google Cluster Workload Trace with 10,000 tasks (CPU load mean: 1.000733 units, data size mean: 1.097850 MB, deadline mean: 15.323545 s).
- **Methods Compared**:
  - **Brute Force (batched)**: Approximates optimal solution in batches.
  - **GA-only**: Population=30, generations=50, tournament=3, mutation=0.1, elitism=2.
  - **SA-only**: Iterations=100, T₀=100, cooling=0.99.
  - **Hybrid GA+SA**: Population=30, generations=100, SA iterations=30 per offspring, T₀=100, cooling=0.99, elitism=2.

| Method       | Cost       | Time (s) | Avg Lat (s) | Energy (J) | Violations | Util Std |
|--------------|------------|----------|-------------|------------|------------|----------|
| Brute Force  | 201,858.27 | 9,497.34 | 9.33        | 108,553.26 | 460        | 4,149.85 |
| GA-only      | 464,218.35 | 5,350.49 | 33.47       | 129,565.49 | 3,138      | 258.28   |
| SA-only      | 492,005.12 | 549.98   | 36.52       | 126,771.36 | 3,462      | 58.00    |
| Hybrid GA+SA | 455,776.14 | 1,746.51 | 32.55       | 130,323.17 | 3,046      | 325.40   |

Plots for each metric are in `results/plots/`. Convergence analysis shows the hybrid GA+SA cost dropping to 484,124.01 units by Generation 3 and reaching 455,776.14 units by Generation 100.

---

## 📜 License

This work is licensed under the [MIT License](LICENSE).

---

## 🤝 Acknowledgments

- Data sourced from the 2019 Google Cluster Workload Trace.
- Thanks to all code reviewers and the open-source community.
- Contributions include enhancing the research paper with citations for key concepts (e.g., IoT architecture, NP-Hardness, GA, SA), adjusting font sizes in the literature comparison table for better readability (larger author names, default-sized headers), and restructuring the Introduction to clearly outline contributions and paper organization.

---

Feel free to file an issue or submit a pull request for improvements!
