# Flux App Tests

Repository for the Flux App functional tests.

## Structure

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
