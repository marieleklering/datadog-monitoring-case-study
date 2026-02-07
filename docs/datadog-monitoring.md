# Datadog Monitoring for SAP HANA Databases on AWS

**A case study on implementing intelligent alerting to reduce alert fatigue and improve monitoring effectiveness**

---

## Overview

This case study documents how we solved alert fatigue issues for SAP HANA database backups running on AWS by implementing Datadog's anomaly detection instead of traditional threshold-based monitoring.

**Technologies:** Datadog, AWS EBS, SAP HANA, Terraform, Anomaly Detection


## Table of Contents

1. Overview
2. The Problem
3. The Solution
4. Results
5. Expanding the Approach
6. Key Takeaways
7. Technical Details

---

## The Problem

### Alert Fatigue from Expected Behavior

The team was experiencing severe alert fatigue from Datadog monitors tracking SAP HANA backup operations. Constant alerts were being received for:

- **EBS surge queue length** spikes during backup operations
- **Disk space utilization** increases on backup volumes

These alerts fired predictably every time backups ran, leading the on-call team to ignore them entirely. This created a dangerous situation: the team was being trained to dismiss alerts that could indicate real problems.

### Why Simple Threshold Adjustments Wouldn't Work

The obvious solution(raising alert thresholds) would create a new problem. If we increased thresholds high enough to avoid alerts during normal backups, we'd miss:

- Backup jobs that started failing or taking longer than expected
- Disk space issues from retention problems
- Performance degradation that manifested gradually
- Infrastructure problems that happened to coincide with backup windows

The solution required alerts that understood the difference between "expected backup behavior" and "something is actually wrong."

---
