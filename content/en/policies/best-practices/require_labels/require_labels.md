---
title: "Require Labels"
category: Best Practices
version: 1.6.0
subject: Pod, Label
policyType: "validate"
description: >
    Define and use labels that identify semantic attributes of your application or Deployment. A common set of labels allows tools to work collaboratively, describing objects in a common manner that all tools can understand. The recommended labels describe applications in a way that can be queried. This policy validates that the label `app.kubernetes.io/name` is specified with some value.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//best-practices/require_labels/require_labels.yaml" target="-blank">/best-practices/require_labels/require_labels.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-labels
  annotations:
    policies.kyverno.io/title: Require Labels
    policies.kyverno.io/category: Best Practices
    policies.kyverno.io/minversion: 1.6.0
    policies.kyverno.io/severity: medium
    policies.kyverno.io/subject: Pod, Label
    policies.kyverno.io/description: >-
      Define and use labels that identify semantic attributes of your application or Deployment.
      A common set of labels allows tools to work collaboratively, describing objects in a common manner that
      all tools can understand. The recommended labels describe applications in a way that can be
      queried. This policy validates that the label `app.kubernetes.io/name` is specified with some value.
spec:
  validationFailureAction: audit
  background: true
  rules:
  - name: check-for-labels
    match:
      any:
      - resources:
          kinds:
          - Pod
    validate:
      message: "The label `app.kubernetes.io/name` is required."
      pattern:
        metadata:
          labels:
            app.kubernetes.io/name: "?*"
```
