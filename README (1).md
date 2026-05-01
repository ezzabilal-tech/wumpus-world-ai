# 🤖 Wumpus World — Knowledge-Based AI Agent

![AI](https://img.shields.io/badge/AI-Knowledge--Based%20Agent-00d4ff?style=flat-square)
![Logic](https://img.shields.io/badge/Logic-Propositional%20Resolution-b06aff?style=flat-square)
![Tech](https://img.shields.io/badge/Tech-HTML%20%7C%20CSS%20%7C%20Vanilla%20JS-ff6b35?style=flat-square)
![Course](https://img.shields.io/badge/Course-Artificial%20Intelligence-00ff88?style=flat-square)
![University](https://img.shields.io/badge/University-FAST%20CFD-ffd700?style=flat-square)

An interactive, browser-based simulation of the classic **Wumpus World** problem from Russell & Norvig's *Artificial Intelligence: A Modern Approach*. The agent uses a propositional logic knowledge base and **resolution refutation** to navigate a hazardous grid, prove cells safe or dangerous, and retrieve the gold.

---

## 📸 Demo

> Open `wumpus_agent.html` directly in any modern browser — no server, no build step, no dependencies.

---

## 🧠 What Is the Wumpus World?

The Wumpus World is a standard benchmark for knowledge-based agents in AI. The agent navigates an N×M grid containing:

| Entity | Symbol | Effect |
|--------|--------|--------|
| **Pit** 🕳️ | Causes breeze in adjacent cells | Agent dies (-1000 pts) |
| **Wumpus** 👾 | Causes stench in adjacent cells | Agent dies (-1000 pts) |
| **Gold** 🏅 | Causes glitter in its cell | Agent wins (+1000 pts) |
| **Breeze** 💨 | Percept | Pit is adjacent |
| **Stench** 🌫️ | Percept | Wumpus is adjacent |
| **Glitter** ✨ | Percept | Gold is here — grab it! |

The agent starts at the bottom-left corner (row R-1, col 0) and must explore safely using only what it can logically prove.

---

## ⚙️ How the Agent Works

### 1. Knowledge Base (KB)

All facts the agent learns are stored as **CNF (Conjunctive Normal Form) clauses**. Every percept triggers logical assertions:

```
¬Breeze(r,c)  ⟹  ¬Pit(nr,nc)  for ALL neighbors (nr,nc)
 Breeze(r,c)  ⟹  Pit(n1) ∨ Pit(n2) ∨ ...  (disjunction over neighbors)
¬Stench(r,c)  ⟹  ¬Wumpus(nr,nc)  for ALL neighbors
 Stench(r,c)  ⟹  Wumpus(n1) ∨ Wumpus(n2) ∨ ...
```

### 2. Resolution Refutation

To prove a literal `L`, the agent:
1. Adds `¬L` (negation of query) to a working copy of the KB
2. Iteratively applies the **resolution rule**: from `(A ∨ B)` and `(¬B ∨ C)` derive `(A ∨ C)`
3. If an **empty clause** (contradiction) is derived → `L` is **proven true**
4. If no new clauses can be generated → `L` cannot be proven from current knowledge

### 3. Cell Classification

Every unvisited neighbor is classified as:

```
safe   → KB ⊨ ¬Pit(r,c)  AND  KB ⊨ ¬Wumpus(r,c)
pit    → KB ⊨  Pit(r,c)
wumpus → KB ⊨  Wumpus(r,c)
unknown → insufficient information
```

### 4. Movement Priority (BFS-guided)

The agent selects its next move by priority:

| Priority | Action |
|----------|--------|
| P1 | Move to a **proven-safe unvisited** direct neighbor |
| P2 | BFS through visited cells to reach a **safe unvisited** cell |
| P3 | Risk an **unknown** direct neighbor (calculated gamble) |
| P4 | BFS through visited cells to reach an **unknown** cell |
| P5 | **Halt** — all reachable cells explored |

---

## 🚀 Getting Started

### Run Locally

```bash
# Clone the repository
git clone https://github.com/YOUR_USERNAME/wumpus-world-ai.git

# Navigate into the project
cd wumpus-world-ai

# Open in browser (no server needed)
open wumpus_agent.html        # macOS
start wumpus_agent.html       # Windows
xdg-open wumpus_agent.html   # Linux
```

That's it. No `npm install`, no build pipeline.

---

## 🕹️ How to Use

1. **Set grid size** — Rows (3–8), Columns (3–8), Pit Count
2. Click **⬡ Initialize World** to generate a random environment
3. Choose speed: Slow / Med / Fast / Turbo
4. Click **▶ Step Once** to advance one decision at a time, or **⚡ Run Auto** to watch the agent navigate autonomously
5. Use **👁 Reveal All** to see the true world layout (ground truth)
6. Monitor the **right panel** for:
   - Live metrics (score, inference count, cells proven safe)
   - KB CNF clause list
   - Step-by-step reasoning trace

---

## 📁 Project Structure

```
wumpus-world-ai/
│
├── wumpus_agent.html       # Complete self-contained application
│   ├── <style>             # Dark-theme UI (CSS custom properties)
│   ├── <body>              # Three-panel layout: controls | grid | reasoning
│   └── <script>
│       ├── World state     # W object — grid, beliefs, KB, score
│       ├── initWorld()     # Random world generation
│       ├── kbTell()        # Assert clauses into KB
│       ├── kbAsk()         # Resolution refutation engine
│       ├── resolveClauses()# Core resolution step
│       ├── classify()      # Infer cell status (safe/pit/wump/unknown)
│       ├── perceive()      # Process arrival at (r,c)
│       ├── agentStep()     # Select and execute next move
│       ├── bfsFirstStep()  # Pathfinding through visited cells
│       ├── move()          # Execute move + handle consequences
│       ├── render()        # Draw the grid
│       └── upd()           # Refresh all UI panels
│
└── README.md
```

---

## 📊 Scoring System

| Event | Points |
|-------|--------|
| Each step taken | −1 |
| Fall into pit | −1000 |
| Eaten by Wumpus | −1000 |
| Grab gold | +1000 |
| Starting score | 1000 |

---

## 🔬 AI Concepts Demonstrated

- **Knowledge-Based Agents** (Russell & Norvig, Ch. 7)
- **Propositional Logic** — symbols, sentences, entailment
- **CNF (Conjunctive Normal Form)** clause representation
- **Resolution Refutation** — complete inference procedure for propositional logic
- **BFS Pathfinding** — navigation through partially-explored graphs
- **Uncertainty & Risk** — principled handling of unknown cells

---

## 🛠️ Technical Details

| Property | Value |
|----------|-------|
| Language | Vanilla JavaScript (ES6+) |
| Dependencies | None (zero external libraries) |
| Grid size | 3×3 up to 8×8 |
| KB safety cap | 5,000 clauses per inference call |
| Resolution iterations | Max 150 per `kbAsk()` call |
| Fonts (CDN) | Orbitron, Share Tech Mono (Google Fonts) |

---

## 📚 Background & Course

This project was developed as part of the **Artificial Intelligence** course at **FAST-NUCES, CFD Campus** (CS, 4th Semester).

It implements the Knowledge-Based Agent from:
> Russell, S., & Norvig, P. (2020). *Artificial Intelligence: A Modern Approach* (4th ed.). Chapter 7 — Logical Agents.

---

## 👤 Author

**[Your Name]**  
BS Computer Science — FAST-NUCES CFD  
4th Semester | AI Course

---

## 📄 License

This project is submitted for academic purposes at FAST-NUCES. Feel free to reference for learning.
