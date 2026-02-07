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

## The Solution

### Anomaly Detection Instead of Static Thresholds

After evaluating options, Datadog's anomaly detection algorithm was implemented for these monitors. This approach:

1. **Learns normal patterns** - The algorithm establishes baselines for expected behavior during backup windows
2. **Adapts over time** - As backup patterns change (longer jobs, more data), the baseline adjusts
3. **Alerts on deviations** - Only triggers when behavior differs significantly from established patterns

### Algorithm Selection: Agile

Datadog's **Agile algorithm** was selected for this use case because:

- **Quick adaptation** - Responds to recent changes in backup patterns
- **Seasonal awareness** - Handles periodic variations (daily backups, weekly full backups)
- **Reduced false positives** - Bounce level prevents alerts from routine fluctuations

### Implementation Approach

The solution was implemented as reusable Terraform modules that could be applied across multiple SAP HANA environments.

**Configuration flexibility:**
```hcl
# Filter monitors by various AWS/infrastructure attributes
hana_backup_surge_queue_monitor_filters = {
  host           = "xxx-prod-01"
  disk           = "/dev/xvdf"
  iam_role       = "xx-xxx-backup"
  security_group = "xx-backup-nodes"
  tags           = ["environment:prod", "app:xxx-xxxx"]
}
```

The Terraform module (`datadog_hana_backup_monitors`) allows specifying:
- Target hosts and disks
- IAM roles, security groups, Chef roles
- Custom tags for filtering

This made it easy to roll out consistent monitoring across multiple environments while maintaining flexibility for environment-specific configurations.

---

## Results

### Before: Alert Fatigue

**Scenario:** Backup starts at 2:00 AM  
**What happened:**
- Monitor triggers alert for EBS surge queue spike
- On-call engineer gets paged
- Alert clears automatically 15 minutes later
- Engineer goes back to sleep
- **Result:** False alarm, wasted on-call time, training team to ignore alerts

### After: Intelligent Alerting

**Normal backup scenario:**
- Backup runs with expected surge queue increase
- Anomaly detection recognizes pattern as normal
- No alert triggered
- On-call engineer sleeps peacefully

**Actual problem scenario:**
- Backup runs but surge queue is 40% higher than normal pattern
- Anomaly detection identifies deviation from baseline
- Alert triggers with context: "Surge queue length anomaly detected"
- Engineer investigates and finds backup job is retrying due to network issues
- **Result:** Real problem caught early, fixed before failure

### Quantifiable Improvements

- **Reduced false positive alerts** from backup operations by ~95%
- **Improved on-call experience** - team stopped ignoring backup-related alerts
- **Faster problem detection** - caught issues that would have been masked by threshold-based alerts
- **Better baseline understanding** - visibility into how backup performance trends over time

---

## Expanding the Approach

### Log Monitoring with Anomaly Detection

Building on the success of surge queue monitoring, we applied the same principles to log monitoring for SAP HANA systems.

#### Implementation

**Log source:** `/var/log/messages` from SAP HANA servers

**Pipeline processing:**
- Parse and normalize log entries
- Extract structured data (severity, component, timestamps)
- Filter out expected noise while retaining signal

**Index strategy:**
- Create focused indexes for critical system and application health logs
- Filter duplicate messages and routine status updates
- Maintain searchability while reducing storage costs

**Anomaly detection on excluded logs:**

Even though certain log types were filtered from primary indexes, they weren't ignored completely. Anomaly detection monitors were implemented to track the *volume* of filtered messages.

**Why this matters:**
If you normally see 10 "connection timeout" warnings per day and suddenly see 1000, that's worth investigating—even if you filtered them out of your primary view.

This approach provided:
- Clean, focused log explorer for daily troubleshooting
- Protection against missing emerging issues in "noisy" log categories
- Reduced alert fatigue from known chattiness
- Early warning system for unusual patterns

---
