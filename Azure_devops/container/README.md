# Container resource

The following azure template use the python container to install requirements and use python(if your machine does not have access to internet use this templates to use python).  

## Requirements

Before proceeding with the installation, ensure you meet the following requirements:

- A Bitbucket account with access to `test/repo`.
- Docker installed on your machine.
- Access to Azure Container Registry (ACR).

## Usage

This project uses Azure Pipelines for Continuous Integration and Deployment (CI/CD) to build and push a Docker image to an Azure Container Registry, and then runs a container based on that image.

### Parameters

The pipeline is configurable through the following parameters:

- `environment`: This parameter is used to specify the target environment. It comes with a default value of `dev`.

  | Parameter    | Description         | Default | Allowed Values       |
  | ------------ | ------------------- | ------- | -------------------- |
  | environment  | Target environment  | dev     | auto, dev, uat, prd  |

**Note**: Do not change the `values` field for the `environment` parameter, as they are used internally in conditional logic.

### Trigger

The pipeline does not trigger automatically:

```yaml
trigger: none
```

### Stages
The pipeline is divided into the following stages:

- Registry: Logs into Azure Container Registry, builds and pushes the Docker image.
- PythonContainer: Spins up a container from the pushed image and runs Python.

```dockerfile
### Dockerfile
The Dockerfile used to build the Python image:

# Use an official Python runtime as the base image
FROM python:3.7-slim

# Set the working directory in the container (optional)
WORKDIR /app

# The ENTRYPOINT sets the command to be run when the container starts.
ENTRYPOINT ["python"]
```