---
title: "Add Quota"
category: Multi-Tenancy, EKS Best Practices
version: 1.6.0
subject: ResourceQuota, LimitRange
policyType: "generate"
description: >
    To better control the number of resources that can be created in a given Namespace and provide default resource consumption limits for Pods, ResourceQuota and LimitRange resources are recommended. This policy will generate ResourceQuota and LimitRange resources when a new Namespace is created.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//best-practices/add_ns_quota/add_ns_quota.yaml" target="-blank">/best-practices/add_ns_quota/add_ns_quota.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-ns-quota
  annotations:
    policies.kyverno.io/title: Add Quota
    policies.kyverno.io/category: Multi-Tenancy, EKS Best Practices
    policies.kyverno.io/subject: ResourceQuota, LimitRange
    policies.kyverno.io/minversion: 1.6.0
    policies.kyverno.io/description: >-
      To better control the number of resources that can be created in a given
      Namespace and provide default resource consumption limits for Pods,
      ResourceQuota and LimitRange resources are recommended.
      This policy will generate ResourceQuota and LimitRange resources when
      a new Namespace is created.
spec:
  rules:
  - name: generate-resourcequota
    match:
      any:
      - resources:
          kinds:
          - Namespace
    generate:
      apiVersion: v1
      kind: ResourceQuota
      name: default-resourcequota
      synchronize: true
      namespace: "{{request.object.metadata.name}}"
      data:
        spec:
          hard:
            requests.cpu: '4'
            requests.memory: '16Gi'
            limits.cpu: '4'
            limits.memory: '16Gi'
  - name: generate-limitrange
    match:
      any:
      - resources:
          kinds:
          - Namespace
    generate:
      apiVersion: v1
      kind: LimitRange
      name: default-limitrange
      synchronize: true
      namespace: "{{request.object.metadata.name}}"
      data:
        spec:
          limits:
          - default:
              cpu: 500m
              memory: 1Gi
            defaultRequest:
              cpu: 200m
              memory: 256Mi
            type: Container
```
