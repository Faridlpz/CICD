# GitVersion

It is a tool that generates a semantic versioning (SemVer) base on its Git history.


## Mainline mode

The version number is typically incremented with each commit to the main branch.

When you commit changes to your main branch, Gitversion will automatically generate a new version number base on the rules.
- Patch: If the commit is just a normal change, GitVersion will increment the patch version. e.g from 1.0.0 to 1.0.0
- Minor: If the commit contains the commit message, such as #minor, GitVersion can increment the Minor. from 1.0.0 to 1.1.0
- Major. This represent a breaking changes

# Semantic Versioning Pipeline

This directory includes an Azure DevOps pipeline configuration for implementing semantic versioning using GitVersion. The pipeline applies versioning to the repository based on Git history and tags.

## Pipeline Overview

The pipeline is designed to calculate semantic version numbers automatically. This process involves the following stages:

- *SemVer*: This stage uses GitVersion to generate a semantic version number based on the repository's Git history.

### Stages and Jobs

The SemVer stage consists of the following jobs and steps:

1. *Versioning*: Determines the semantic version and tags the repository.

    - Checkout the code with credentials persisted.
    - Download and extract GitVersion tool.
    - Verify the current version using GitVersion configuration.
    - Set the calculated version tag as a pipeline variable.
    - Create a Git tag with the version and push it to the repository.
    - Cleanup the GitVersion tool artifacts.

### GitVersion Configuration

The pipeline leverages a GitVersion configuration file which is used to define the versioning strategy. The configuration file looks like this:

```yaml
mode: ContinuousDelivery
branches:
  main:
    regex: ^main$
    is-mainline: true
    source-branches: [ 'develop', 'release' ]
  develop:
    regex: ^develop?[/-]
    tag: alpha
    source-branches: []
  feature:
    regex: ^features?[/-]
    tag: alpha
    source-branches: []
ignore:
  sha: []
commit-message-incrementing: Enabled
```

### Usages

To use this pipeline, you should have the GitVersion file located in the path you work with. Then te pipeline will then use this file to calculate the semantic versioning number

### Prerequisites
- Azure devops account and permissions
- GitVersion tool compatible with the agent pool
- Git configured with credentials to push tags