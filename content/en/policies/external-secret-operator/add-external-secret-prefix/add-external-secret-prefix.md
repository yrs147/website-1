---
title: "Add prefix to exernal secret"
category: ExternalSecretOperator
version: 1.6.0
subject: ExternalSecret
policyType: "mutate"
description: >
    This Policy mutates secretRef key to add a prefix. External Secret Operator proposes to use kyverno to force ExternalSecrets to have namespace prefix so that kubernetes administrators do not need to define permissions and users per namespace. Doing this developers are abstracted by administrators naming convention and will not  be able to access secrets from other namespaces. In this example, in the JSON patch change "prefix-" to your preferred prefix. For example: {{ request.namespace }}
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//external-secret-operator/add-external-secret-prefix/add-external-secret-prefix.yaml" target="-blank">/external-secret-operator/add-external-secret-prefix/add-external-secret-prefix.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-external-secret-prefix
  annotations:
    policies.kyverno.io/title: Add prefix to exernal secret
    policies.kyverno.io/category: ExternalSecretOperator
    policies.kyverno.io/severity: medium
    policies.kyverno.io/subject: ExternalSecret
    kyverno.io/kyverno-version: 1.7.1
    policies.kyverno.io/minversion: 1.6.0
    kyverno.io/kubernetes-version: "1.23"
    policies.kyverno.io/description: >- 
      This Policy mutates secretRef key to add a prefix.
      External Secret Operator proposes to use kyverno to force ExternalSecrets
      to have namespace prefix so that kubernetes administrators do not need to
      define permissions and users per namespace.
      Doing this developers are abstracted by administrators naming convention and will not 
      be able to access secrets from other namespaces.
      In this example, in the JSON patch change "prefix-" to your preferred prefix. For example: {{ request.namespace }}
spec:
  rules:
  - name: add-external-secret-prefix
    match:
      any:
      - resources:
          kinds:
          - ExternalSecret
    mutate:
      foreach: 
      - list: "request.object.spec.data"
        patchesJson6902: |-
          - path: /spec/data/{{elementIndex}}/remoteRef
            op: add
            value: {
              "key" : "prefix-{{element.remoteRef.key}}", 
              "property": {{element.remoteRef.property}},
              "conversionStrategy": {{element.remoteRef.conversionStrategy}},
              "decodingStrategy": {{element.remoteRef.decodingStrategy}}
            } 

```
