
# Context and Design Decisions

*This section documents the content design decisions for the Datadog Anomaly Detection case study, for portfolio review purposes.*

Read the full documentation → [Datadog Monitoring for SAP HANA Databases on AWS](../docs/datadog-monitoring.md)

---

## Research Foundation

This case study documents a real implementation from production infrastructure work managing SAP HANA environments on AWS. Unlike fictional portfolio pieces, the constraints here were real: the monitoring had to work reliably for on-call engineers losing sleep over false alerts, and the solution had to be maintainable across multiple environments.

The problem it addresses is not specific to SAP HANA or AWS. Alert fatigue from predictable, expected system behavior is a recurring pattern across infrastructure environments. The same issue appears wherever monitoring is configured by people who understand the system but not the operational experience of the people responding to it at 2 AM. That gap between configuration and lived experience informed every documentation decision here.

This case study was written for a technical audience — engineers and hiring managers who can evaluate both the implementation and the thinking behind it.

---

## Design Rationale

**Leading with the human problem**

The doc could have opened with the technology — anomaly detection, Terraform, SAP HANA. Instead it opens with engineers being woken up three times a night for alerts that meant nothing. This was a deliberate choice. A reader scanning a portfolio piece decides quickly whether the writer understands the problem or just the tooling. Leading with the operational cost of bad monitoring design signals that the solution was driven by real pain, not technical preference. The tradeoff is that readers looking for a quick technical reference have to read through context first. That is acceptable — this doc is written for people evaluating judgment, not people copying config.

**Preempting the obvious objection**

Any engineer reading this would immediately think "why not just raise the threshold?" The section addressing this exists to answer that question before it gets asked. Skipping it would have made the solution feel like the first idea tried rather than the right one. The four failure modes listed are not exhaustive — they are the ones most likely to resonate with someone who has lived through on-call rotations and watched a threshold adjustment create a new problem instead of solving the original one.

**Narrative scenarios over a metrics table**

The results section uses before/after scenarios rather than a table of numbers. This was a deliberate choice. A table would have shown the 95% reduction in false positives cleanly, but it would have stripped out the human context that makes the number meaningful. An engineer reading "3 pages per night reduced to near zero" understands the impact differently than reading "95% reduction." The scenario format also makes the distinction between a normal backup and an actual problem concrete, which is the entire point of the solution. The tradeoff is that scenarios take longer to read than a table. That is acceptable when the goal is understanding, not scanning.

**The filtered logs insight placed mid-section**

The observation that filtered logs should still be monitored for anomalies is one of the more valuable ideas in the document. It is placed mid-section rather than elevated to a standalone section because it is an extension of the core principle, not a separate one. Promoting it would have implied it was a different solution. Keeping it embedded signals that anomaly detection on noise is the same thinking applied consistently, not a new idea bolted on.

**Admitting iteration and imperfection**

Most case studies present clean success. Takeaway four acknowledges that the monitors did not work correctly on day one and required adjustment. This was kept in deliberately. A reader evaluating judgment wants to know whether the writer understands that good monitoring is built through observation, not perfect initial configuration. Removing this section would have made the case study more polished and less credible.

---
