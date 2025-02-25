---
title: "Spread Pods Across Nodes"
category: Sample
version: 1.6.0
subject: Deployment, Pod
policyType: "mutate"
description: >
    Deployments to a Kubernetes cluster with multiple availability zones often need to distribute those replicas to align with those zones to ensure site-level failures do not impact availability. This policy matches Deployments with the label `distributed=required` and mutates them to spread Pods across zones.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other/spread_pods_across_topology/spread_pods_across_topology.yaml" target="-blank">/other/spread_pods_across_topology/spread_pods_across_topology.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: spread-pods
  annotations:
    policies.kyverno.io/title: Spread Pods Across Nodes
    policies.kyverno.io/category: Sample
    policies.kyverno.io/subject: Deployment, Pod
    policies.kyverno.io/minversion: 1.6.0
    policies.kyverno.io/description: >-
      Deployments to a Kubernetes cluster with multiple availability zones often need to
      distribute those replicas to align with those zones to ensure site-level failures
      do not impact availability. This policy matches Deployments with the label
      `distributed=required` and mutates them to spread Pods across zones.
spec:
  rules:
    - name: spread-pods-across-nodes
      # Matches any Deployment with the label `distributed=required`
      match:
        any:
        - resources:
            kinds:
            - Deployment
            selector:
              matchLabels:
                distributed: required
      # Mutates the incoming Deployment.
      mutate:
        patchStrategicMerge:
          spec:
            template:
              spec:
                # Adds the topologySpreadConstraints field if non-existent in the request.
                +(topologySpreadConstraints):
                - maxSkew: 1
                  topologyKey: zone
                  whenUnsatisfiable: DoNotSchedule
                  labelSelector:
                    matchLabels:
                      distributed: required
```
