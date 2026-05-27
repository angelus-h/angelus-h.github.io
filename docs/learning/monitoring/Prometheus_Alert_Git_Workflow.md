# Git Workflow: Adding Prometheus Alert

Guide for adding a new Prometheus alert (Example: `PrometheusPodHighMemoryUsage`)

## 1. Branch Setup

Create a new feature branch for your alert:

```bash
git checkout -b prometheus-pod-memory-alert
```

## 2. Making Changes

### Update Alert Files

- Add or modify the alert YAML file
- Update the corresponding promtool test file

**Example files:**
- Alert: `rhobs/alerting/data_plane/prometheus.prometheus_pod_high_memory_usage_alert.yaml`
- Test: `test/promql/tests/data_plane/high_pod_memory_usage_alert_test.yaml`

### Check Status

```bash
$ git status
```

**Output:**
```
On branch prometheus-pod-memory-alert
Your branch is up to date with 'origin/prometheus-pod-memory-alert'.

Changes not staged for commit:
modified:  prometheus.prometheus_pod_high_memory_usage_alert.yaml
modified:  test/promql/tests/data_plane/high_pod_memory_usage_alert_test.yaml
```

### Stage Changes

```bash
git add rhobs/alerting/data_plane/prometheus.prometheus_pod_high_memory_usage_alert.yaml
git add test/promql/tests/data_plane/high_pod_memory_usage_alert_test.yaml
```

## 3. Committing Changes

Commit with a descriptive message following conventional commits:

```bash
git commit -m "feat(OPS-4586): create new memory usage alert of prometheus instances" \
-m "" \
-m "Signed-off-by: Karishma Karna <kkarna@company.com>"
```

**Output:**
```
[prometheus-pod-memory-alert bc176d1] Update the component in test and alert file
2 files changed, 2 insertions(+)
```

### Push to Remote

```bash
git push origin prometheus-pod-memory-alert
```

## 4. Rebasing and Squashing Commits

Use interactive rebase to squash multiple commits into a single clean commit:

```bash
git rebase -i 049bb515e06cb0d2b553fde500d76c0890a6880a~1
```

### Interactive Editor

In the editor, mark commits to squash:

```
pick 049bb51 Add PrometheusPodHighMemoryUsage alert with unit tests
squash bc176d1 Update the component in test and alert file
squash 14efe84 feat(OPS-4586): create new memory usage alert of prometheus instances
```

Save and close. Git will combine the commits and allow you to edit the final commit message.

**Output:**
```
Successfully rebased and updated refs/heads/prometheus-pod-memory-alert.
```

### Force Push

```bash
git push --force-with-lease origin prometheus-pod-memory-alert
```

!!! warning "Force Push Safety"
Always use `--force-with-lease` instead of `--force` to avoid overwriting others' work.

## 5. Running promtool Tests

Verify your alert with promtool unit tests:

```bash
promtool test rules test/promql/tests/data_plane/high_pod_memory_usage_alert_test.yaml
```

**Expected output:**
```
✓ SUCCESS
```

## 6. Summary

**Complete workflow:**

1. Branch created
2. Alert and test files updated
3. Changes committed with proper message
4. Interactive rebase for squash
5. Force push with `--force-with-lease`
6. Promtool tests verified

## Related Resources

- [Prometheus Alerting Rules Documentation](https://prometheus.io/docs/prometheus/latest/configuration/alerting_rules/)
- [Promtool Testing Guide](https://prometheus.io/docs/prometheus/latest/configuration/unit_testing_rules/)
- [Conventional Commits](https://www.conventionalcommits.org/)

---

**Author:** Karishma Karna
**Ticket:** OPS-4586
**Date:** March 2025
