
# Context and Design Decisions

*This section documents the content design decisions for the Datadog Anomaly Detection case study, for portfolio review purposes.*

Read the full documentation → [Datadog Monitoring for SAP HANA Databases on AWS](../docs/datadog-monitoring.md)

---

## Research Foundation

This case study documents a real implementation from production infrastructure work managing SAP HANA environments on AWS. Unlike fictional portfolio pieces, the constraints here were real: the monitoring had to work reliably for on-call engineers losing sleep over false alerts, and the solution had to be maintainable across multiple environments.

The problem it addresses is not specific to SAP HANA or AWS. Alert fatigue from predictable, expected system behavior is a recurring pattern across infrastructure environments. The same issue appears wherever monitoring is configured by people who understand the system but not the operational experience of the people responding to it at 2 AM. That gap between configuration and lived experience informed every documentation decision here.

This case study was written for a technical audience — engineers and hiring managers who can evaluate both the implementation and the thinking behind it.
