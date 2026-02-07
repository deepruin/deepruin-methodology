# DeepRuin Methodology

> Open-source frameworks for network intelligence and influence analysis.

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)

---

## 🎯 Overview

Traditional CRMs track *contacts*. We track *relationships*.

This repository contains our open-source methodology for:
- Modeling relationship depth and quality
- Tracking relationship trajectories over time
- Identifying optimal paths through professional networks
- Predicting relationship health and reactivation timing

## 🧠 Core Framework: Relationship Depth Model

Inspired by Facebook's Cicero AI and game-theoretic approaches to social reasoning.

![Relationship Depth Schema](assets/relationship_schema.gif)

### The 6 Layers

| Layer | Purpose | Key Fields |
|-------|---------|------------|
| **🌱 Origin** | How the relationship started | `how_met`, `when`, `context`, `introduced_by` |
| **📊 Interactions** | Communication patterns | `frequency`, `reciprocity`, `channels`, `response_time` |
| **🤝 Trust** | Relationship strength signals | `level (1-5)`, `signals`, `favors_given`, `favors_received` |
| **📈 State** | Current health | `current`, `trajectory`, `reactivation_attempts` |
| **🎯 Strategic** | Mutual value | `value_to_me`, `value_to_them`, `mutual_interests` |
| **🔮 Cicero Signals** | Predictive modeling | `intent_model`, `cooperation_probability`, `best_approach` |

### Why "Cicero"?

Facebook's Cicero AI mastered Diplomacy by modeling other players' beliefs and intentions. We apply similar thinking to professional relationships:

- **Intent modeling** — What does this person want? What are they optimizing for?
- **Cooperation probability** — How likely are they to help if asked?
- **Best approach** — What communication style works with this person?

## 📊 Network Health Tracking

Real-time monitoring of relationship trajectories:

```
🟢 Active    — Interaction within 7 days
🟡 Warm      — Interaction within 30 days  
🟠 Cooling   — Interaction within 90 days
🔴 Dormant   — No recent interaction
⚪ No data   — Never logged
```

### Health Dashboard Example

```
Status | Name                 | Company       | Last Touch | 30d | Strength
-------|----------------------|---------------|------------|-----|----------
  🟢   | Samantha Walsh       | ESL Invest    | 2026-02-06 |   3 | 5
  🟢   | Collen Roller        | NVIDIA        | 2026-02-05 |   2 | 5
  🟡   | Emily Pastewka       | Anthropic     | 2026-01-15 |   1 | 4
  🔴   | Angela Fan           | OpenAI        | 2025-08-11 |   0 | 4
```

### Alerts

The system generates alerts when high-value relationships need attention:

```
🚨 Angela Fan (OpenAI)
   Strength: 4 | Alert: No contact in 180+ days
   Recommendation: Low-pressure reactivation, no ask
```

## 🎯 Engagement Channel Analysis

Not all channels are equal. We score reachability across:

| Channel | Engagement Type | Signal Strength |
|---------|-----------------|-----------------|
| **Signal/SMS** | Private, direct | Highest |
| **Email** | Professional, async | High |
| **LinkedIn DM** | Professional, semi-public | Medium |
| **Twitter/X** | Public, broadcast | Low-Medium |
| **LinkedIn Post** | Public, broadcast | Low |

### Multi-Channel Scoring

```json
{
  "signal": {"reachable": true, "group": "Inner Circle"},
  "linkedin": {"active": true, "responsive": true},
  "twitter": {"handle": "@example", "active": false},
  "email": {"reachable": true, "response_time_hours": 24}
}
```

## 🗃️ Database Schema

### Contacts Table
```sql
CREATE TABLE contacts (
  id UUID PRIMARY KEY,
  names JSONB,        -- {first, last, middle, nicknames[], suffixes[]}
  emails JSONB,       -- [{address, type, primary}]
  phones JSONB,       -- [{number, type, primary}]
  companies JSONB,    -- [{name, role, current, started, ended}]
  tags TEXT[],
  engagement JSONB,   -- Multi-channel engagement data
  notes TEXT
);
```

### Relationships Table
```sql
CREATE TABLE relationships (
  id UUID PRIMARY KEY,
  from_contact UUID REFERENCES contacts(id),
  to_contact UUID REFERENCES contacts(id),
  type VARCHAR(50),   -- knows, worked_with, reports_to, invested_in
  strength INTEGER,   -- 1-5 scale
  context TEXT,
  depth JSONB         -- Full Cicero-inspired depth model
);
```

### Relationship Events Table
```sql
CREATE TABLE relationship_events (
  id UUID PRIMARY KEY,
  contact_id UUID REFERENCES contacts(id),
  event_type VARCHAR(50),  -- interaction, state_change, milestone
  event_date TIMESTAMP,
  channel VARCHAR(50),     -- signal, email, linkedin, call
  direction VARCHAR(20),   -- inbound, outbound, mutual
  sentiment VARCHAR(20),   -- positive, neutral, negative
  notes TEXT
);
```

## 📈 Graph Analysis

We use graph databases (FalkorDB/Redis Graph) for:

### Pathfinding
```cypher
MATCH p=shortestPath(
  (a:Person {name: 'You'})-[:KNOWS*..4]-(b:Person {name: 'Target'})
)
RETURN p
```

### Centrality Analysis
```cypher
MATCH (p:Person)-[r:KNOWS]-()
RETURN p.name, count(r) as connections
ORDER BY connections DESC
```

### Community Detection
Identify clusters of highly-connected individuals for targeted engagement.

## 🛠️ Implementation

Reference implementations available in the [workspace repository](https://github.com/deepruin/deepruin-workspace):

- `scripts/relationship_tracker.js` — CLI for logging interactions
- `scripts/export_graph.js` — Export to graph formats
- `scripts/analyze_graph.py` — NetworkX/StellarGraph analysis
- `db/003_relationship_depth.sql` — Full schema

## 📚 Further Reading

- [Facebook Cicero Paper](https://ai.meta.com/research/cicero/) — AI for Diplomacy
- [Game Theory in Networks](https://en.wikipedia.org/wiki/Network_science) — Academic foundations
- [Social Capital Theory](https://en.wikipedia.org/wiki/Social_capital) — Sociological basis

## 🤝 Contributing

We welcome contributions! Areas of interest:
- Additional Cicero signals and intent modeling
- Reactivation timing algorithms
- Visualization tools
- Integration with CRM platforms

## 📜 License

MIT License — use freely, attribution appreciated.

---

*Building the science of professional relationships.*
