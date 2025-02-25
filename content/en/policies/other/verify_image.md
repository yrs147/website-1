---
title: "Verify Image"
category: Software Supply Chain Security, EKS Best Practices
version: 1.7.0
subject: Pod
policyType: "verifyImages"
description: >
    Using the Cosign project, OCI images may be signed to ensure supply chain security is maintained. Those signatures can be verified before pulling into a cluster. This policy checks the signature of an image repo called ghcr.io/kyverno/test-verify-image to ensure it has been signed by verifying its signature against the provided public key. This policy serves as an illustration for how to configure a similar rule and will require replacing with your image(s) and keys.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other/verify_image.yaml" target="-blank">/other/verify_image.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: verify-image
  annotations:
    policies.kyverno.io/title: Verify Image
    policies.kyverno.io/category: Software Supply Chain Security, EKS Best Practices
    policies.kyverno.io/severity: medium
    policies.kyverno.io/subject: Pod
    policies.kyverno.io/minversion: 1.7.0
    policies.kyverno.io/description: >-
      Using the Cosign project, OCI images may be signed to ensure supply chain
      security is maintained. Those signatures can be verified before pulling into
      a cluster. This policy checks the signature of an image repo called
      ghcr.io/kyverno/test-verify-image to ensure it has been signed by verifying
      its signature against the provided public key. This policy serves as an illustration for
      how to configure a similar rule and will require replacing with your image(s) and keys.
spec:
  validationFailureAction: enforce
  background: false
  rules:
    - name: verify-image
      match:
        any:
        - resources:
            kinds:
              - Pod
      verifyImages:
      - imageReferences:
        - "ghcr.io/kyverno/test-verify-image*"
        mutateDigest: true
        attestors:
        - entries:
          - keys:
              publicKeys: |
                -----BEGIN PUBLIC KEY-----
                MFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAE8nXRh950IZbRj8Ra/N9sbqOPZrfM
                5/KAQN0/KjHcorm/J5yctVd7iEcnessRQjU917hmKO6JWVGHpDguIyakZA==
                -----END PUBLIC KEY-----

```
