---
title: "Add Default Resources"
category: Other
version: 1.6.0
subject: Pod
policyType: "mutate"
description: >
    Pods which don't specify at least resource requests are assigned a QoS class of BestEffort which can hog resources for other Pods on Nodes. At a minimum, all Pods should specify resource requests in order to be labeled as the QoS class Burstable. This sample mutates any container in a Pod which doesn't specify memory or cpu requests to apply some sane defaults.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other/add_default_resources/add-default-resources.yaml" target="-blank">/other/add_default_resources/add-default-resources.yaml</a>

```yaml
apiVersion : kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-default-resources
  annotations:
    policies.kyverno.io/title: Add Default Resources
    policies.kyverno.io/category: Other
    policies.kyverno.io/severity: medium
    kyverno.io/kyverno-version: 1.6.0
    policies.kyverno.io/minversion: 1.6.0
    kyverno.io/kubernetes-version: "1.23"
    policies.kyverno.io/subject: Pod
    policies.kyverno.io/description: >-
      Pods which don't specify at least resource requests are assigned a QoS class
      of BestEffort which can hog resources for other Pods on Nodes. At a minimum,
      all Pods should specify resource requests in order to be labeled as the QoS
      class Burstable. This sample mutates any container in a Pod which doesn't
      specify memory or cpu requests to apply some sane defaults.
spec:
  background: false
  rules:
  - name: add-default-requests
    match:
      any:
      - resources:
          kinds:
          - Pod
    preconditions:
      any:
      - key: "{{request.operation || 'BACKGROUND'}}"
        operator: AnyIn
        value:
        - CREATE
        - UPDATE
    mutate:
      patchStrategicMerge:
        spec:
          containers:
            - (name): "*"
              resources:
                requests:
                  +(memory): "100Mi"
                  +(cpu): "100m"
```
