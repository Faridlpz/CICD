# Azure DevOps Pipeline Configuration

This README provides an explanation of the Azure DevOps Pipeline configuration provided in the YAML file.

## Requirements
- Azure DevOps Pipeline configured with appropriate permissions to access GitHub repositories and Kubernetes clusters.
- Helm installed in the Azure DevOps build agent environment.

## Usage
1. **Configure Parameters**: If necessary, modify the parameters defined in the pipeline YAML file to suit your deployment requirements. For example, you can change the target environment or override values for Helm charts.

2. **Set Up Resources**: Ensure that the GitHub repository containing external templates (`InsertValue/InsertValue`) is accessible to the Azure DevOps Pipeline.

3. **Trigger Pipeline**: Manually trigger the Azure DevOps Pipeline or set up a scheduled trigger according to your deployment needs.

4. **Review Pipeline Output**: Monitor the execution of the pipeline and review the output to ensure successful deployment of Helm charts to the Kubernetes cluster.

## Pipeline Configuration Details

### Trigger
No trigger is defined (`trigger: none`), indicating that this pipeline is not triggered automatically but can be manually triggered.

### Parameters
- **environment**: Defines the target environment for deployment. The default value is `dev`, and allowed values are `dev` and `uat`.

### Resources
Defines a GitHub repository as a resource to pull in external templates for the pipeline.

### Stages and Jobs
- **Stage: Microservice**: Defines a stage named `Microservice` for deploying microservices.
  - **Job: Helm_templates**: Defines a job named `Helm_templates` responsible for deploying Helm charts.
    - **Step: Template**: Uses an external template stored in the GitHub repository to set up an AKS cluster.
    - **Task: HelmInstaller**: Installs the latest version of Helm.
    - **Script**: Packages the Helm chart named `microservice` into a .tgz file.
    - **Task: HelmDeploy**: Deploys the Helm chart to the Kubernetes cluster.
      - `connectionType`: Specifies the type of Kubernetes service connection.
      - `KubernetesServiceEndpoint`: Defines the Kubernetes service endpoint for deployment.
      - `command`: Specifies the Helm command to execute (`upgrade` in this case).
      - `chartType`: Specifies the type of chart path (`FilePath`).
      - `chartPath`: Specifies the path to the Helm chart .tgz file.
      - `releaseName`: Specifies the release name for the Helm deployment.
      - `overrideValues`: Specifies the YAML file containing override values for the Helm chart.
      - `arguments`: Additional arguments to pass to the Helm command.
