---
title: "Unique Ingress Host"
category: Sample
version: 1.6.0
subject: Ingress
policyType: "validate"
description: >
    An Ingress host is a URL at which services may be made available externally. In most cases, these hosts should be unique across the cluster to ensure no routing conflicts occur. This policy checks an incoming Ingress resource to ensure its hosts are unique to the cluster. It also ensures that only a single host may be specified in a given manifest.      
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other/restrict_ingress_host/restrict_ingress_host.yaml" target="-blank">/other/restrict_ingress_host/restrict_ingress_host.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: unique-ingress-host
  annotations:
    policies.kyverno.io/title: Unique Ingress Host
    policies.kyverno.io/category: Sample
    policies.kyverno.io/severity: medium
    policies.kyverno.io/subject: Ingress
    policies.kyverno.io/minversion: 1.6.0
    policies.kyverno.io/description: >-
      An Ingress host is a URL at which services may be made available externally. In most cases,
      these hosts should be unique across the cluster to ensure no routing conflicts occur.
      This policy checks an incoming Ingress resource to ensure its hosts are unique to the cluster.
      It also ensures that only a single host may be specified in a given manifest.      
spec:
  validationFailureAction: audit
  background: false
  rules:
    - name: check-single-host
      match:
        any:
        - resources:
            kinds:
              - Ingress
      context:
        - name: hosts
          apiCall:
            urlPath: "/apis/networking.k8s.io/v1/ingresses"
            jmesPath: "items[].spec.rules[].host"
      preconditions:
        all:
        - key: "{{ request.operation || 'BACKGROUND' }}"
          operator: Equals
          value: CREATE
        - key: "{{ request.object.spec.rules[].host }}"
          operator: AnyIn
          value: "{{ hosts }}"
      validate:
        message: "The Ingress host name must be unique."
        deny: {}
    - name: deny-multiple-hosts
      match:
        any:
        - resources:
            kinds:
              - Ingress
      preconditions:
        all:
        - key: "{{ request.operation || 'BACKGROUND' }}"
          operator: Equals
          value: CREATE
        - key: "{{ request.object.spec.rules[].host | length(@)}}"
          operator: GreaterThan
          value: 1
      validate:
        message: "An Ingress resource may only contain a single host entry."
        deny: {}

```
