# Hybrid-Genetic-Algorithm-and-Simulated-Annealing-for-Task-Scheduling


```markdown
# Hybrid GA+SA for Edge–Fog–Cloud Task Scheduling

This repository contains the code, data, and document sources for our research on  
**Hybrid Genetic Algorithm + Simulated Annealing** (GA+SA) to schedule IoT tasks across  
three compute tiers: **Edge**, **Fog**, and **Cloud**.  

---

## 📄 Overview

Modern IoT applications generate thousands of small tasks that can be run on:

- **Edge devices** (sensors, smartphones) — very low latency (<10 ms), limited CPU/memory/battery  
- **Fog nodes** (local gateways, micro data centers) — moderate latency (10–50 ms), moderate resources  
- **Cloud servers** (remote data centers) — high latency (50–200 ms), high compute capacity  

Assigning _N_ tasks to these 3 tiers yields \(3^N\) possible schedules (NP‑Hard).  
Exact search only works for very small N. We therefore propose a **two‐stage heuristic**:

1. **Genetic Algorithm (GA)** — global exploration via population evolution (selection, crossover, mutation, elitism)  
2. **Simulated Annealing (SA)** — local refinement starting from the best GA solution, escaping local minima via Metropolis acceptance  

We generate **10 000 synthetic tasks** (uniformly random CPU loads, data sizes, deadlines) and compare:

- **Brute Force** (first 12 tasks only)  
- **GA‐only**  
- **SA‐only**  
- **Hybrid GA+SA**  

across six metrics:  
`Total Cost` (weighted sum of latency+energy), `Runtime`, `Average Latency`, `Total Energy`,  
`Deadline Violations`, and `Utilization Imbalance`.  

Our hybrid GA+SA finds near‐optimal schedules (within 1–2 %) in seconds—a huge speedup over exhaustive search.

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

3. **Run the Jupyter notebooks** in `notebooks/` for step‑by‑step experiments:  
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
  \[
    \text{Latency}(t_i,τ)
    = \frac{w_i}{C_τ}
    + L_{src→τ}
    + \frac{d_i}{B_{src→τ}}
  \]

- **Energy model**  
  Utilization 
  \(\displaystyle u_τ=\frac{\sum w_i}{C_τ}\).  
  Energy 
  \(\displaystyle E_τ(u)=P_{idle,τ}+(P_{peak,τ}-P_{idle,τ})\,u_τ\).

- **Objective**  
  Minimize 
  \(\alpha\sum \text{Latency} + \beta\sum E\),  
  with trade‑off weights \(\alpha,\beta\).  

- **NP‑Hardness**  
  The problem generalizes the Generalized Assignment Problem; exact search is \(\mathcal O(3^N)\).

- **Random seeds**  
  All experiments fix `random.seed(42)` and `np.random.seed(42)` for reproducibility.

- **Chromosome encoding**  
  A list of length `N`, each gene ∈ `{0,1,2}` mapping to `['edge','fog','cloud']`.

---

## 🧪 Experiments & Results

- **Brute Force**: Exhaustive search on first 12 tasks → ground‑truth cost  
- **GA-only**: population=30, generations=50, tournament=3, mutation=0.02, elitism=2  
- **SA-only**: iterations=100, T₀=100, cooling=0.95  
- **Hybrid GA+SA**: GA→SA pipeline  

| Method       | Cost   | Time (s) | Avg Lat (s) | Energy (J) | Violations | Util Std |
|--------------|--------|----------|-------------|------------|------------|----------|
| Brute Force  | 1317.1 | 395.3    | 22.20       | 1050.8     | 7          | 26.29    |
| GA-only      | 1402.4 | 12.5     | 24.35       | 1102.1     | 12         | 28.07    |
| SA-only      | 1525.8 | 2.1      | 26.54       | 1125.4     | 15         | 30.12    |
| Hybrid GA+SA | 1338.7 | 14.8     | 22.87       | 1075.3     | 8          | 26.80    |

Plots for each metric are in `results/plots/`.

---

## 📜 License

This work is licensed under the [MIT License](LICENSE).

---

## 🤝 Acknowledgments

- Synthetic data generation inspired by real IoT workload studies  
- Thanks to all code reviewers and the open‐source community  

---

Feel free to file an issue or submit a pull request for improvements!
