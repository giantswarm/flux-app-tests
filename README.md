# Flux App Tests

Repository for the Flux App functional tests.

## Structure Proposal 1 (focused on tests)

The below structure groups test cases, with input and expected results.

```
bases
└── BASE_RESOURCES
    └── BASE_RESOURCE_NAME
        ├── kustomization.yaml
        └── ...
tests
├── run.yaml
└── test_cases
    └── TEST_CASE_NAME
        ├── input
        |   ├── kustomization.yaml
        |   └── ...
        └── result
            └── ...
```

### The `bases` directory

Groups Kustomize's bases for various resources. These resources constitute the fundamental building blocks for the test cases.

### The `BASE_RESOURCES` directory

Groups Kustomize's bases for a single resource type. Provides additional granularity for bases management. At the time of writing this document, only the below types are considered.

* `apps`

### The `BASE_RESOURCE_NAME` directory

Name of the specific resource uniquely identifying and differentiating it from other resource of the same type. This directory contains the `kustomization.yaml` and other Kustomize-supported files denoted by the `...`.

### The `tests` directory

Groups test cases. Applying the output of `run` executes the tests, that is, it configures the Flux to use this repository as a source and apply everything that's located in the `tests/test_cases/**/input` directory.

### The `TEST_CASE_NAME` directory

Single test case. The name should briefly describe it.

### The `input` directory

Collects the input expected to be reconciled by Flux. It stands the actual test case.

### The `result` directory

Gathers results the test should produce. It is here for comparison purposes.


## Structure Proposal 2 (focused MC-to-WC relation)

At the moment of writing this document, the repository follows the below structure.

```
bases
└── BASE_RESOURCES
    └── BASE_RESOURCE_NAME
        ├── kustomization.yaml
        └── ...
management-clusters
└── MC_NAME
    └── overlays
        └── OVERLAY_RESOURCES
            └── OVERLAY_RESOURCE_NAME
                ├── kustomization.yaml
                ├── ...
                └── WC_NAME
                    ├── kustomization.yaml
                    └── ...
```

### The `bases` directory

Groups Kustomize's bases for various resources. These resources constitute the fundamental building blocks for a complete environment.

### The `BASE_RESOURCES` directory

Groups Kustomize's bases for a single resource type. Provides additional granularity for bases management. At the time of writing this document, only the below types are considered.

* `apps`

### The `BASE_RESOURCE_NAME` directory

Name of the specific resource uniquely identifying and differentiating it from other resource of the same type. This directory contains the `kustomization.yaml` and other Kustomize-supported files denoted by the `...`.

### The `management-clusters` directory

Groups MCs this repository hosts the configuration for.

### The `MC_NAME` directory

Name of the specific MC. This name does not necessarily need to reflect the actual MC name, but should uniquely identify it and differentiate from other MCs.

### The `overlays` directory

Groups Kustomize's overlays for various resources. These overlays are then put on top of the bases to produce final manifests expected to be deployed.

### The `OVERLAY_RESOURCES` directory

Groups Kustomize's overlays for a single resource type. Provides additional granularity for overlays management. It reflect the `BASE_RESOURCES` directory content. At the time of writing this document, only the below types are considered.

* `apps`

### The `OVERLAY_RESOURCE_NAME` directory

Name of the specific resource uniquely identifying and differentiating it from other resource of the same type. If the installation targets the MC cluster itself, this directory may contains the `kustomization.yaml` and other Kustomize-supported files denoted by the `...`.

### The `WC_NAME` directory

Name of the specific WC. This is for grouping resources targeting a WC, even though the manifests are deployed on the MC. This name does not necessarily need to reflect the actual WC name, but should uniquely identify it and differentiate from other WCs.

## Sample Flux Configuration

The sample configuration presented here assumes the existence of the following asset:

* `test-mc` management cluster (MC),
* `test` organization and corresponding `org-test` namespace on the MC,
* `test-wc` workload cluster (WC),
* `github-credentials` available on the MC inside the `org-test` namespace.

The resources in the next sections configures the WCs with the apps listed in their respective directories.

### The `GitRepository` CR

```
apiVersion: source.toolkit.fluxcd.io/v1beta1
kind: GitRepository
metadata:
  name: flux-app-tests
  namespace: org-test
spec:
  interval: 1m
  url: https://github.com/giantswarm/flux-app-tests
  secretRef:
    name: github-credentials
  ref:
    branch: feature/init-with-data
```

### The `Kustomization` CR

```
apiVersion: kustomize.toolkit.fluxcd.io/v1beta1
kind: Kustomization
metadata:
  name: test-apps
  namespace: org-test
spec:
  prune: true
  interval: 1m
  path: "./management-clusters/test-mc/overlays/apps"
  sourceRef:
    kind: GitRepository
    name: flux-app-tests
  timeout: 2m
```
