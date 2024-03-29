# Newman API Testing with Azure DevOps

This repository contains files for setting up Newman API testing in Azure DevOps.

## Files Explanation

### `templates/newman-template-jobs.yml`

This YAML file defines a job template for running API tests with Newman in Azure DevOps. It contains steps for installing Newman, running API tests, and publishing test results.

```yaml
jobs:
  - job: API_Test
    displayName: API Testing Job
    steps:
      - script: |
          echo Installing Newman
          sudo npm install -g newman
        workingDirectory: $(Build.SourcesDirectory)/Azure_devops/newman/
        displayName: Install newman

      - script: |
          newman run $(Build.SourcesDirectory)/Azure_devops/newman/collection.json -d $(Build.SourcesDirectory)/Azure_devops/newman/data.json --reporters cli,junit --reporter-junit-export $(Build.SourcesDirectory)/Azure_devops/newman/junitReport.xml
        workingDirectory: $(Build.SourcesDirectory)/
        displayName: Run API Tests
        continueOnError: true

      - task: PublishTestResults@2
        displayName: Publish Test Results $(Build.SourcesDirectory)/Azure_devops/newman/*.xml
        inputs:
          testResultsFiles: $(Build.SourcesDirectory)/Azure_devops/newman/*.xml
```

### `collection.json`
This file contains a sample Postman collection with two requests: a dummy GET request and a dummy POST request.

```collection
{
  // Sample API Collection
  "item": [
    {
      "name": "Dummy GET Request",
      "request": {
        "method": "GET",
        "url": "https://api.example.com/get"
      }
    },
    {
      "name": "Dummy POST Request",
      "request": {
        "method": "POST",
        "url": "https://api.example.com/post",
        "body": "{\"key\": \"value\"}"
      }
    }
  ]
}
```

### `data.json`
This file contains sample data for use in API tests.


```data
{
  // Sample API Collection
  "item": [
    {
      "name": "Dummy GET Request",
      "request": {
        "method": "GET",
        "url": "https://api.example.com/get"
      }
    },
    {
      "name": "Dummy POST Request",
      "request": {
        "method": "POST",
        "url": "https://api.example.com/post",
        "body": "{\"key\": \"value\"}"
      }
    }
  ]
}
```

### `newman.yml`
This YAML file defines an Azure DevOps pipeline to trigger API testing using the Newman job template.


```newman
trigger: none

stages:  
  - stage: API_Testing
    pool: 
      vmImage: 'ubuntu-20.04'
    jobs:
      - template: templates/newman-template-jobs.yml

```

### Usage

To use this project for API testing with Newman in Azure DevOps, follow these steps:

- Fork or clone this repository to your Azure DevOps project.
- Ensure you have the necessary permissions in your Azure DevOps project.
- Modify the collection.json and data.json files to suit your API testing needs.
- Create an Azure DevOps pipeline and link it to your repository.
- Choose the existing newman.yml file as the pipeline configuration.
- Run the pipeline to trigger API testing with Newman.

### Requirements
To use this project, you need:

- An Azure DevOps account with sufficient permissions to create and run pipelines.
- Newman installed globally on your build agent to run API tests.
