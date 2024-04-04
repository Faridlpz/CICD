# mapkubeapis

The use of this pipeline help you to change the deprecated version of hpa and changes to the current one


# Files Explanation

## deployment-dev.yml

```yaml
- name: test
  deployments:
    - deployment-test
```

## Usage:
This file is commonly utilized for specifying deployment configurations in YAML format. It outlines the deployment named test along with its associated deployment deployment-test.

## Deprecated-apis.yaml
```mapping
mappings:
  - deprecatedAPI: "apiVersion: autoscaling/v2beta2\nkind: HorizontalPodAutoscaler\n"
    newAPI: "apiVersion: autoscaling/v2\nkind: HorizontalPodAutoscaler\n"
    deprecatedInVersion: "v1.23"
    removedInVersion: "v1.25"
```
## Usage:
It's employed for managing mappings of deprecated Kubernetes APIs to their newer equivalents. This file provides crucial information about deprecated APIs and their lifecycle stages.

## mapping-pipeline-template.yml
```azuretemplate
parameters:
  - name: environment
    displayName: ambiente destino
    default: dev
    values: ['auto','dev', 'uat', 'prd']
  - name: region
    displayName: Seleccione la region
    default: EU
    values: ['EU', 'CENTRAL']

trigger: none

stages:
  - stage: mapkubeapis
    ${{ if eq(parameters.region, 'EU') }}:
      jobs:
      - job: mapkubeapis_eu
        workspace:
          clean: all
        steps:
          # Steps for EU region
```
## Usage:
It's intended to be a blueprint for creating Azure Pipelines tasked with mapping Kubernetes APIs. The parameters such as environment and region offer flexibility for various deployment scenarios.