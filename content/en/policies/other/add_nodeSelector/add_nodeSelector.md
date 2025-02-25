---
title: "Add nodeSelector"
category: Sample
version: 1.6.0
subject: Pod
policyType: "mutate"
description: >
    The nodeSelector field uses labels to select the node on which a Pod can be scheduled. This can be useful when Pods have specific needs that only certain nodes in a cluster can provide. This policy adds the nodeSelector field to a Pod spec and configures it with labels `foo` and `color`.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other/add_nodeSelector/add_nodeSelector.yaml" target="-blank">/other/add_nodeSelector/add_nodeSelector.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-nodeselector
  annotations:
    policies.kyverno.io/title: Add nodeSelector
    policies.kyverno.io/category: Sample
    policies.kyverno.io/subject: Pod
    policies.kyverno.io/minversion: 1.6.0
    policies.kyverno.io/description: >-
      The nodeSelector field uses labels to select the node on which a Pod can be scheduled.
      This can be useful when Pods have specific needs that only certain nodes in a cluster can provide.
      This policy adds the nodeSelector field to a Pod spec and configures it with labels `foo` and `color`.
spec:
  rules:
  - name: add-nodeselector
    match:
      any:
      - resources:
          kinds:
          - Pod
    # Adds the `nodeSelector` field to any Pod with two labels.
    mutate:
      patchStrategicMerge:
        spec:
          nodeSelector:
            foo: bar
            color: orange
```
