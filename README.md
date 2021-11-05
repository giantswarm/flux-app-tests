# Flux App Tests

Repository for the Flux App functional tests.

## Structure Proposal (focused on tests)

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

## Executing Tests

In order to execute all the tests at once run:

```
./tests/run | kubectl apply -f -
```

In order to execute only a specific test, run:

```
./tests/run TEST_CASE_NAME | kubectl apply -f -
```

In order to simply validate the result, run:

```
kubectl diff -f tests/test_cases/TEST_CASE_NAME/result/RESOURCE.yaml --field-manager=kubectl-server-side-apply
```

In order to delete the tests, run:

```
./tests/run | kubectl delete -f -
```
