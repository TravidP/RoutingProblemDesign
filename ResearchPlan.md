# Thesis Summary and Deep Research Plan

**Source thesis:** Yusuf Saltan, *Designing Non-Monetary Incentives for Efficient Selfish Routing through Strategic Intersection Control*, M.S. thesis, Bilkent University, December 2025.

**Local source file:** [`ReferencePaper/Yuself thesis Master Bilkent.pdf`](ReferencePaper/Yuself%20thesis%20Master%20Bilkent.pdf)

**Research focus:** robust, fair, mixed-autonomy timestamp incentives for selfish routing.

**Last literature check:** 2026-05-22.

---

## 1. Executive Summary

The thesis studies a clean but powerful idea: urban intersections should not be treated only as bottlenecks. With connected and autonomous intersection management, intersections can become **non-monetary control points** that shape drivers' route choices by changing waiting time instead of charging money.

In classical selfish routing, each driver chooses the route that minimizes their own travel time. This produces a Wardrop user equilibrium, but the equilibrium can be worse than the system-optimal traffic pattern. Monetary tolls can theoretically fix this inefficiency, but tolls are politically difficult, expensive to administer, and often unfair to lower-income travelers. Saltan's thesis asks whether intersections can create equivalent incentives using **delay control**.

The answer is yes, under a structured model. The thesis proposes two mechanisms:

1. **Strategic Priority-Based Scheduling (SPBS):** vehicles on socially preferred routes receive higher priority at an autonomous intersection, while vehicles on overused routes receive lower priority.
2. **Timestamp-offset incentives:** a planner slightly advances or delays vehicles' effective request timestamps before roadside units schedule intersection crossing.

Both mechanisms create **path-dependent node costs**. A driver still acts selfishly, but the route costs they observe are reshaped so that selfish behavior becomes closer to the socially efficient flow.

The strongest empirical result in the thesis is on the Sioux Falls benchmark: timestamp incentives close about **68.2%** of the user-equilibrium optimality gap under high-intervention timestamp bounds. A later arXiv version of the same research line, posted as version 2 on April 18, 2026, reports up to **71%** efficiency-gap reduction under realistic constraints.

The best next research direction is:

> **Robust, fair, mixed-autonomy timestamp incentives for selfish routing.**

This direction keeps the thesis' core contribution but makes it more publishable and realistic by handling dynamic demand, partial connected/autonomous vehicle penetration, fairness, privacy, spillback, and scalable bilevel optimization.

---

## 2. Thesis Idea in Simple Terms

Imagine two routes from an origin to a destination. One route is individually attractive, so many drivers choose it. But when everyone chooses it, total congestion becomes worse than if some drivers had taken the other route. This is the classic selfish-routing problem.

The usual economic fix is a toll: make the overused route more expensive so some drivers switch. Saltan's thesis replaces money with **time priority at intersections**.

Instead of saying:

> "Pay more to use this route."

the system says:

> "If you use this socially helpful route, your intersection request may be processed earlier. If you use the overused route, your request may be processed slightly later."

The intervention does not need new roads or monetary payments. It uses a control capability that future autonomous intersections already need: deciding the order in which vehicles pass through conflict zones.

The conceptual loop is:

1. Drivers choose routes selfishly.
2. Selfish choices create an inefficient user equilibrium.
3. Autonomous intersections observe requests, timestamps, movements, and possibly planned routes.
4. A planner changes priority or effective timestamps.
5. These local delay changes alter perceived route costs.
6. Drivers respond by redistributing across routes.
7. Total system travel time decreases.

---

## 3. How the Mechanisms Work

### 3.1 Mechanism A: Strategic Priority-Based Scheduling

SPBS is the simpler mechanism. It is introduced on Pigou-type networks, a standard stress test for selfish routing.

Under a first-come-first-served baseline, all vehicles are treated symmetrically at the intersection. If the lower route in Pigou's network looks individually faster, selfish drivers overuse it, creating inefficient congestion.

Under SPBS:

- Vehicles submit route information when entering the communication zone.
- The roadside unit assigns a dynamic priority score.
- Vehicles on socially preferred routes receive higher base priority.
- Vehicles on overused routes receive lower base priority.
- The scheduler still respects safety and physical queue feasibility.

The thesis models this as a route-dependent node delay:

```text
upper route node delay = w - p
lower route node delay = w + p
```

Here, `w` is the nominal intersection delay and `p` is the priority-induced waiting-time difference. Even small values of `p` can change equilibrium route choice because drivers compare complete path costs, not only road-segment costs.

**Contribution of SPBS:** it shows that intersection priority can behave like a non-monetary toll in a stylized network, and SUMO simulation supports that the qualitative effect survives microscopic vehicle and queue dynamics.

### 3.2 Mechanism B: Timestamp-Offset Incentives

The second mechanism generalizes the SPBS idea.

In autonomous intersection management, vehicles send request packets to roadside units. A request can include:

- vehicle identifier,
- timestamp,
- intended movement,
- route or partial route information,
- approach lane and state information.

The planner modifies the effective timestamp before the local roadside unit schedules the vehicle.

- A **timestamp advancement** makes a vehicle appear earlier in the queue.
- A **timestamp delay** makes a vehicle appear later.
- The local roadside unit still handles safety and collision-free scheduling.

This creates a path-dependent node cost:

```text
node cost for path p at intersection v = baseline node delay at v + timestamp offset for path p at v
```

In compact notation:

```text
c_v^p(f_v, u_v^p) = c_v(f_v) + u_v^p
```

where:

- `f_v` is the total flow through intersection `v`,
- `c_v(f_v)` is the baseline congestion-dependent node delay,
- `u_v^p` is the timestamp incentive applied to path `p` at node `v`.

This matters because the cost remains additive and structured. The model can still use congestion-game tools, potential functions, and equilibrium optimization instead of becoming an unstructured simulation-only controller.

---

## 4. Main Contribution Outline

### Contribution 1: Intersections as Mechanism-Design Interfaces

The thesis reframes intersections as active incentive devices. Instead of only minimizing local delay, an intersection can intentionally shape network-level route choice.

This is important because most urban delay is node-based: signals, queues, merges, and conflict resolution at intersections often dominate travel time.

### Contribution 2: Non-Monetary Alternative to Tolls

The mechanisms mimic the role of tolls without financial transfers. This avoids several deployment barriers:

- billing infrastructure,
- equity concerns,
- political resistance,
- privacy-sensitive payment systems,
- driver opposition to explicit road pricing.

The cost is still real: some vehicles receive slightly more delay. But the "currency" is controlled waiting time, not money.

### Contribution 3: SPBS Analytical and Simulation Evidence

The SPBS chapter shows, in Pigou's network, that route-dependent priority can shift selfish equilibrium away from an overused congestible route. The thesis derives equilibrium and social-optimum expressions under the simplified node-cost model, then validates the qualitative behavior in SUMO.

### Contribution 4: Timestamp Incentives as Path-Dependent Node Costs

The timestamp mechanism extends the idea from one stylized intersection to general networks. The key modeling step is to represent timestamp changes as additive path-dependent node costs. This lets the analysis retain a potential-game structure.

### Contribution 5: Equilibrium Existence and Essential Uniqueness

For fixed incentives, the induced routing game admits a potential function:

```text
Phi(f, u) =
  sum over edges e of integral_0^{f_e} c_e(z) dz
  + sum over nodes v of integral_0^{f_v} c_v(y) dy
  + f dot u
```

Under the thesis assumptions, Wardrop equilibria exist, and all equilibria have the same social cost. This avoids ambiguity when evaluating an incentive policy.

### Contribution 6: Bilevel Incentive Design

The planner chooses timestamp offsets while anticipating selfish driver response:

- **Upper level:** choose incentives to minimize resulting social cost.
- **Lower level:** drivers settle into Wardrop equilibrium under modified path costs.

This is a Stackelberg-style formulation: the planner acts first, drivers respond.

### Contribution 7: Microscopic-to-Macroscopic Calibration

The thesis uses SUMO to calibrate realistic intersection node-delay functions for the Sioux Falls network. Each of the 24 intersections is simulated, and the resulting delays are fitted with quartic node-cost models.

The calibrated model then combines:

- BPR-style link costs,
- quartic node costs,
- timestamp-offset incentives,
- Frank-Wolfe equilibrium computation,
- SPSA upper-level optimization.

### Contribution 8: Network-Level Efficiency Gains

On Sioux Falls:

- baseline user-equilibrium cost: about `8.04 x 10^6` minutes,
- system-optimal benchmark: about `7.75 x 10^6` minutes,
- high-intervention timestamp incentives: about `7.84 x 10^6` minutes,
- thesis-reported gap closure: about **68.2%**.

The thesis therefore demonstrates that bounded non-monetary intersection incentives can produce substantial network-level gains.

---

## 5. Mathematical and Algorithmic Workflow

The thesis can be read as a pipeline:

### Step 1: Model the Road Network

Represent the network as a directed graph:

```text
G = (V, E)
```

where:

- `V` is the set of intersections,
- `E` is the set of road links,
- `P` is the set of origin-destination paths,
- `f_p` is the fraction of users choosing path `p`.

### Step 2: Compute Flow-Induced Costs

Each path cost combines:

- link travel time,
- intersection delay,
- timestamp incentive.

```text
path cost = sum(link costs) + sum(node costs + offsets)
```

### Step 3: Characterize Selfish Routing

A Wardrop equilibrium occurs when every used path has minimal perceived cost. No infinitesimal driver can improve by switching routes.

This is the lower-level behavior.

### Step 4: Preserve Potential-Game Structure

Because timestamp offsets are additive, the equilibrium can be characterized as the minimizer of a convex potential function under monotone cost assumptions.

This is the main technical reason the mechanism remains analytically tractable.

### Step 5: Optimize Incentives

The planner chooses the incentive vector `u` to reduce the social cost of the equilibrium induced by `u`.

The thesis solves this using:

- Frank-Wolfe for user-equilibrium assignment,
- SPSA for gradient-free upper-level incentive tuning,
- SUMO-generated calibration data for node costs.

### Step 6: Validate in Simulation

The thesis links microscopic and macroscopic models:

- SUMO estimates realistic intersection delay curves.
- Quartic functions approximate node delays.
- Sioux Falls tests whether the mechanism changes network-wide route allocation.

---

## 6. Limitations and Open Gaps

The thesis is strong because it creates a clean, analyzable foundation. The next research step is to relax the assumptions that make the theory clean.

| Gap | Why It Matters | Research Opportunity |
|---|---|---|
| Static nonatomic routing | Real traffic evolves over time and vehicles are discrete | Build dynamic timestamp incentives with stability guarantees |
| Full or partial route observability | Drivers may not reveal full routes, and privacy laws may restrict data use | Design prefix-based or privacy-preserving incentives |
| Separable monotone node costs | Real intersections have spillback, blocking, and lane coupling | Extend the theory to capacity-constrained dynamic traffic assignment |
| High V2I capability assumption | Many networks will have mixed human-driven and connected/autonomous vehicles | Analyze penetration thresholds and partial compliance |
| Perfectly selfish rational drivers | Humans are boundedly rational and learn through navigation apps | Combine timestamp incentives with behavioral equilibrium models |
| Fairness not deeply modeled | Delay incentives may repeatedly penalize some OD pairs or neighborhoods | Add equity constraints and rotating delay budgets |
| Expensive bilevel optimization | Large networks may require too many equilibrium solves | Develop differentiable or surrogate-assisted bilevel solvers |
| Strategic misreporting | Drivers or routing platforms may manipulate timestamps or route reports | Create truthful or manipulation-resistant protocols |

---

## 7. Latest Literature Map

This map focuses on 2023-2026 work that can deepen the thesis into a publishable research program.

### 7.1 Direct Thesis Line

| Work | Link | Why It Matters |
|---|---|---|
| Saltan, Kosay, Lin, and Sayin, "Strategic Control of Intersections for Efficient Traffic Routing without Tolls," IFAC-PapersOnLine, 2024/2025 | <https://doi.org/10.1016/j.ifacol.2025.01.176> | Direct precursor to the SPBS part of the thesis. It frames priority-based intersection scheduling as a non-monetary routing incentive. |
| Kosay, Saltan, Wang, Lin, and Sayin, "Controlling Traffic without Tolls: A Non-Monetary Framework for Autonomous Intersections," arXiv v2, 2026-04-18 | <https://arxiv.org/abs/2511.01421> | Newer version of the timestamp-offset framework. It reports up to 71% efficiency-gap reduction, compared with the thesis' 68.2% Sioux Falls result. |

### 7.2 Non-Monetary and Credit-Based Incentives

| Work | Link | Why It Matters |
|---|---|---|
| Ghafelebashi, Razaviyayn, and Dessouky, "Congestion reduction via personalized incentives," Transportation Research Part C, 2023 | <https://doi.org/10.1016/j.trc.2023.104153> | Provides a benchmark for app-mediated personalized incentives using aggregate traffic and user preferences. |
| van de Sanden, Schoukens, and Salazar, "A Data-driven Pricing Scheme for Optimal Routing through Artificial Currencies," IFAC World Congress, 2023 | <https://arxiv.org/abs/2211.14793> | Artificial currencies are a close cousin of non-monetary timestamp incentives and introduce learning-based incentive adaptation. |
| Ding, Yang, Qin, and Xu, "Credit charge-cum-reward scheme for green multi-modal mobility," Transportation Research Part B, 2023 | <https://doi.org/10.1016/j.trb.2023.102852> | Useful for comparing delay incentives against tradable-credit and reward mechanisms under heterogeneous values of time. |
| Pedroso, Agazzi, Heemels, and Salazar, "Fair Artificial Currency Incentives in Repeated Weighted Congestion Games," arXiv, 2024 | <https://arxiv.org/abs/2403.03999> | Gives a fairness lens: equity versus equality in non-monetary resource-allocation incentives. |

### 7.3 Information Design and Bounded Rationality

| Work | Link | Why It Matters |
|---|---|---|
| Griesbach, Hoefer, Klimm, and Koglin, "Information Design for Congestion Games with Unknown Demand," AAAI, 2024 | <https://doi.org/10.1609/aaai.v38i9.28830> | Shows how signaling can influence equilibrium when demand is uncertain. Timestamp incentives could be paired with information design. |
| Massicot and Langbort, "On Network Congestion Reduction Using Public Signals Under Boundedly Rational User Equilibria," arXiv, 2024 | <https://arxiv.org/abs/2406.00295> | Important because drivers may choose paths within an indifference band rather than exact shortest paths. |
| Li and Duan, "Human-in-the-loop Learning for Dynamic Congestion Games," IEEE Transactions on Mobile Computing / arXiv, 2024 | <https://arxiv.org/abs/2404.15599> | Models route choice as a learning process and proposes non-monetary informational mechanisms to reduce inefficient exploration. |

### 7.4 Mixed Autonomy, CAV Control, and Intersections

| Work | Link | Why It Matters |
|---|---|---|
| Nisyrios and Gkiotsalitis, "Optimization-based Approaches for Traffic and Fleet Management of Connected and Autonomous Vehicles: A Systematic Literature Review," 2025 | <https://doi.org/10.1007/s13177-025-00536-2> | Maps optimization-based CAV traffic management and highlights mixed-traffic challenges. |
| Xie, Fan, Yau, and Xiao, "A survey on safe and efficient control for autonomous vehicles at unsignalized intersections," 2025 | <https://doi.org/10.1177/09544070251377108> | Useful for safety, control, and evaluation methods at unsignalized autonomous intersections. |
| Wu, Wang, Yin, and Lynch, "Participatory traffic control: Leveraging connected and automated vehicles to enhance network efficiency," Transportation Research Part C, 2024 | <https://doi.org/10.1016/j.trc.2024.104757> | Shows how a subset of participating CAVs can influence broader network behavior, which is central for partial adoption. |
| Park, Zhang, Wang, Wang, and Jiang, "Integrated Routing and Traffic Signal Control for CAVs via Reinforcement Learning Approach," IEEE ITSC, 2024 | <https://impact.ornl.gov/en/publications/integrated-routing-and-traffic-signal-control-for-cavs-via-reinfo/> | Useful baseline for learning-based joint routing and signal control. |
| "Group-benefit control strategy for connected automated vehicles in mixed traffic at intersections," Transportation Research Part C, 2025 | <https://doi.org/10.1016/j.trc.2025.105121> | Uses multi-agent deep reinforcement learning for mixed traffic at signalized intersections, relevant to dynamic and heterogeneous extensions. |
| "Fault-tolerant collaboration: A hierarchical control framework for traffic-communication systems at intersections with human-machine hybrid driving," Expert Systems with Applications, 2026 | <https://doi.org/10.1016/j.eswa.2026.131656> | Relevant for robust right-of-way assignment under communication failures and mixed human-machine driving. |

### 7.5 Robustness and Spillback-Aware Routing

| Work | Link | Why It Matters |
|---|---|---|
| Toso, Frasca, and Kibangou, "Selfish routing on transportation networks with supply and demand constraints," arXiv, 2024 | <https://arxiv.org/abs/2412.20449> | Extends selfish routing toward cell-transmission-style capacity constraints, important for spillback-aware timestamp incentives. |
| Chiu, Li, and Ferguson, "Distributionally Robust Tolls for Traffic Networks with Affine Latency Functions," arXiv, 2026 | <https://arxiv.org/abs/2604.16447> | Although toll-based, it gives a robust optimization template for incentives under latency-model uncertainty. |

---

## 8. Deep Research Plan

### Recommended Main Topic

**Robust, fair, mixed-autonomy timestamp incentives for selfish routing**

This topic is attractive because it keeps the thesis' core novelty while moving toward real deployment. The thesis establishes that timestamp incentives can work in a clean model. A deeper research program should ask:

> Can timestamp incentives still improve network efficiency when only some vehicles are connected, demand changes over time, users are boundedly rational, and fairness/privacy constraints must be respected?

### Research Question 1: Dynamic Online Timestamp Incentives

**Question:** Can timestamp offsets adapt in real time under time-varying OD demand, incidents, and prediction error?

**Motivation:** The thesis optimizes mostly offline, equilibrium-based incentives. Real traffic is dynamic. Demand changes by time of day, incidents happen, and queue states matter.

**Proposed method:**

- Model traffic over rolling horizons.
- Use dynamic traffic assignment or a cell-transmission model for network propagation.
- Let the planner update timestamp offsets every few minutes.
- Add queue-stability and maximum-delay constraints.
- Compare model predictive control, online learning, and differentiable equilibrium methods.

**Expected contribution:**

- A dynamic timestamp-incentive mechanism with provable stability or regret bounds.
- SUMO or CityFlow validation under incidents and time-varying demand.

**Evaluation metrics:**

- total travel time,
- queue length,
- spillback frequency,
- convergence of route choices,
- efficiency gap versus dynamic system optimum,
- robustness under demand prediction error.

### Research Question 2: Mixed Autonomy and Partial Compliance

**Question:** How much CAV/V2I penetration is needed before non-monetary intersection incentives produce network-wide gains?

**Motivation:** Full autonomous intersection management is not an immediate deployment assumption. Near-term networks will include human drivers, connected human-driven vehicles, automated vehicles, transit vehicles, freight, cyclists, and pedestrians.

**Proposed method:**

- Split users into classes:
  - connected compliant vehicles,
  - connected strategic vehicles,
  - non-connected human drivers,
  - navigation-app users,
  - priority vehicles such as buses or emergency vehicles.
- Model only a subset of vehicles as timestamp-controllable.
- Let non-participants respond indirectly to changed congestion.
- Analyze penetration thresholds where incentives become beneficial.

**Expected contribution:**

- A heterogeneous equilibrium model for timestamp incentives under partial adoption.
- Threshold results for CAV penetration and compliance.
- Practical guidance for staged deployment.

**Evaluation metrics:**

- social cost improvement versus adoption rate,
- user-class-specific delay,
- route redistribution,
- participant versus non-participant welfare,
- sensitivity to compliance errors.

### Research Question 3: Fairness-Aware Delay Incentives

**Question:** Can delay-based incentives improve efficiency without repeatedly penalizing the same users, OD pairs, or neighborhoods?

**Motivation:** Non-monetary does not automatically mean fair. If the same commuters repeatedly receive timestamp delays because their route is systemically overused, the mechanism may become politically unacceptable.

**Proposed method:**

- Introduce fairness constraints:
  - maximum average added delay per OD pair,
  - rolling delay budgets,
  - max-min welfare,
  - envy-style constraints,
  - neighborhood-level equity constraints.
- Compare efficiency-only and fairness-constrained timestamp incentives.
- Study rotating priority or artificial-credit hybrids.

**Expected contribution:**

- A fair non-monetary tolling framework.
- Quantified efficiency-equity trade-off curves.
- A policy-ready interpretation of who gains and who loses.

**Evaluation metrics:**

- total travel time,
- Gini coefficient of added delay,
- worst-case OD delay,
- share of users harmed beyond a threshold,
- fairness-constrained price of anarchy.

### Research Question 4: Privacy-Preserving and Manipulation-Resistant Incentives

**Question:** Can the planner design useful path-dependent incentives without requiring full route disclosure, and what happens if users misreport route or timestamp data?

**Motivation:** The strongest form of the thesis mechanism assumes route-dependent incentives. But full route disclosure may be unacceptable or unavailable. Also, once delay incentives matter, strategic users may manipulate reports.

**Proposed method:**

- Replace full routes with partial observability:
  - next movement only,
  - prefix-based route classes,
  - probabilistic route beliefs,
  - privacy-noised route reports.
- Design incentives that work with limited information.
- Add misreporting models:
  - false route declaration,
  - timestamp spoofing,
  - identity cycling,
  - navigation-platform manipulation.
- Study screening, audit, cryptographic commitment, or anomaly detection.

**Expected contribution:**

- A privacy-compatible timestamp mechanism.
- A manipulation-resistant intersection incentive protocol.
- Bounds on the value of route information.

**Evaluation metrics:**

- efficiency loss from limited observability,
- privacy-utility trade-off,
- misreporting gain,
- detection rate,
- incentive compatibility gap.

### Research Question 5: Scalable Bilevel Algorithms

**Question:** Can network-wide timestamp incentives be optimized without tens of thousands of equilibrium solves?

**Motivation:** The thesis uses Frank-Wolfe for lower-level equilibrium and SPSA for upper-level optimization. This is practical for medium networks but may become expensive for city-scale systems.

**Proposed method:**

- Replace gradient-free upper-level search with:
  - implicit differentiation through equilibrium conditions,
  - adjoint Frank-Wolfe,
  - differentiable traffic assignment,
  - graph neural network surrogates,
  - warm-started equilibrium solvers,
  - decomposition by corridor or district.
- Compare exact, approximate, and learned optimizers.

**Expected contribution:**

- A fast bilevel solver for timestamp incentive design.
- Error bounds or empirical approximation guarantees.
- Large-network experiments beyond Sioux Falls.

**Evaluation metrics:**

- social cost,
- solver time,
- number of equilibrium calls,
- approximation error,
- scalability with number of nodes, OD pairs, and paths.

---

## 9. Recommended First Paper

The strongest first-paper formulation is:

> **Fair and robust timestamp incentives for mixed-autonomy selfish routing.**

### Core Claim

Bounded timestamp incentives can reduce selfish-routing inefficiency even when only a subset of vehicles is connected, provided the planner accounts for fairness constraints and uncertain compliance.

### Minimal Technical Scope

Start with a static model that extends the thesis in only two directions:

1. heterogeneous controllability,
2. fairness-constrained timestamp bounds.

Avoid solving dynamic spillback, privacy, and strategic misreporting in the first paper. Those can become later papers.

### Paper Structure

1. **Introduction:** intersections as non-monetary toll interfaces.
2. **Model:** mixed connected/non-connected users, path-dependent node costs, fairness constraints.
3. **Theory:** equilibrium existence and performance/fairness trade-off.
4. **Algorithm:** bilevel optimization with warm-started Frank-Wolfe and constrained upper-level search.
5. **Experiments:** Pigou, Braess, Sioux Falls, and one grid network.
6. **Discussion:** deployment implications and limitations.

### Main Baselines

- no incentive / FCFS,
- SPBS priority rule,
- unconstrained timestamp incentive,
- fairness-constrained timestamp incentive,
- marginal-cost tolling as an idealized monetary benchmark,
- information-only routing recommendation if available.

---

## 10. Recommended 90-Day Plan

### Days 1-15: Reproduce and Simplify

- Reproduce Pigou/SPBS equations from the thesis.
- Implement a small Wardrop equilibrium solver.
- Verify user equilibrium, social optimum, and Price of Anarchy on Pigou and Braess networks.
- Write a short technical memo explaining how timestamp offsets alter path costs.

### Days 16-30: Build the Sioux Falls Baseline

- Load Sioux Falls network and OD demand.
- Implement or reuse BPR link costs.
- Add node-cost functions from the thesis, if coefficient data is available.
- Compute:
  - baseline user equilibrium,
  - system optimum,
  - efficiency gap.

### Days 31-45: Add Timestamp Incentives

- Implement bounded path-dependent timestamp offsets.
- Use Frank-Wolfe for lower-level equilibrium.
- Use a simple constrained upper-level optimizer.
- Confirm that the model can reproduce the thesis-style gap closure directionally.

### Days 46-60: Add One Deep Extension

Choose **one** extension for the first paper:

- mixed autonomy,
- fairness constraints,
- dynamic demand,
- privacy-limited observability,
- spillback-aware capacity constraints.

Recommended first choice: **fairness constraints under mixed autonomy**.

### Days 61-75: Run Experiments

- Run Pigou and Braess tests for interpretability.
- Run Sioux Falls for benchmark credibility.
- Run sensitivity analysis:
  - incentive bound,
  - CAV penetration,
  - compliance rate,
  - fairness budget,
  - demand perturbation.

### Days 76-90: Write the First Paper Draft

- Create figures:
  - cost versus incentive bound,
  - efficiency versus fairness,
  - improvement versus CAV penetration,
  - flow redistribution maps.
- Write theorem/proposition statements.
- Compare against recent literature.
- Prepare a 6-8 page conference-paper version.

---

## 11. Suggested Thesis-Level Roadmap

| Chapter/Paper | Topic | Core Contribution |
|---|---|---|
| Paper 1 | Fair mixed-autonomy timestamp incentives | Shows timestamp incentives still work with partial CAV penetration and bounded unfairness |
| Paper 2 | Dynamic online timestamp control | Adds time-varying demand, incidents, and receding-horizon optimization |
| Paper 3 | Privacy and strategic reporting | Removes full route-disclosure assumptions and studies manipulation resistance |
| Paper 4 | Spillback-aware theory | Extends the model from separable node costs to capacity-coupled dynamic queues |
| Paper 5 | Scalable bilevel algorithms | Builds a city-scale differentiable or surrogate-assisted optimizer |

---

## 12. Final Takeaway

Saltan's thesis is valuable because it identifies a new interface between traffic control and mechanism design:

> **Intersection scheduling can be used as an incentive mechanism.**

The key research opportunity is not merely to improve the existing Sioux Falls result. It is to make the idea robust enough for realistic urban networks where drivers are heterogeneous, only some vehicles are connected, fairness matters, route information is incomplete, and queues interact dynamically.

The most promising next step is therefore a focused first paper on:

> **Robust and fair timestamp incentives for mixed-autonomy selfish routing.**

This direction is technically deep, connected to the latest literature, and close enough to the thesis foundation to be implemented and validated within a realistic research timeline.

