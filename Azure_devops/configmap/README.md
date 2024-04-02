## Parameters
Defines the parameters that can be used when running this pipeline. These parameters allow customization of the pipeline behavior.

- **environment**: Specifies the target environment. Default value is `dev`. Allowed values are `auto`, `dev`, `uat`, and `prd`.
- **region**: Specifies the region. Default value is `eu`. Allowed values are `eu` and `central`.
- **configmap**: Specifies the name of the configmap.
- **namespace**: Specifies the namespace name.

## Trigger
Specifies the condition under which the pipeline should be triggered. In this case, the trigger is set to `none`, meaning the pipeline will not be automatically triggered but will need to be manually initiated.

## Resources
Defines the external repositories that the pipeline will interact with.

- **utils**: Bitbucket repository containing utility scripts or resources.
- **configmap**: Bitbucket repository containing configuration files for configmaps.

## Stages
Defines the stages of the pipeline.

### Configmap
This stage is responsible for creating or updating configmaps.

- **Pool**: Specifies the pool of agents to be used for this stage. In this case, it uses an Ubuntu 20.04 agent.

- **Jobs**: Specifies the jobs to be executed within this stage.

  - **configmap_eu** or **configmap_central**: Jobs executed based on the selected region.

    - **Steps**: Actions to be performed within the job.

      - **Checkout**: Clones the specified repository.
      - **PersistCredentials**: Persists the credentials for accessing the repository.
      - **Kubernetes Login**: Logs in to the Kubernetes cluster based on the environment and region.
      - **Helm Deploy**: Deploys Helm charts to the Kubernetes cluster (only for `central` region and `prd` environment).
      - **Script**: Modifies configmap files, applies them to the Kubernetes cluster, and creates a backup if necessary.
      - **Script**: Retrieves configmap details from the Kubernetes cluster.

