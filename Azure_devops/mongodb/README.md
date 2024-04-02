## Azure Pipeline for MongoDB Document Insertion

### Usage Requirements
- This Azure Pipeline is designed to insert documents into a MongoDB collection.
- It assumes you have a MongoDB database set up and accessible.

### Parameters
- `environment`: Allows selection of the environment (dev, uat, prd) where the documents will be inserted.
- `fileName`: Specifies the name of the JavaScript file containing the document insertion script.

### Trigger
- `trigger: none`: This pipeline is not triggered by any event, implying that it will be manually triggered.

### Resources
- Two repositories are defined: `cosmodb` and `utils` from Bitbucket. These repositories likely contain necessary scripts and configurations for the pipeline.

### Stages
- **Documents**: Defines a stage named "Documents" where the insertion job will be executed.
  - **Variables**: Specifies environment-specific variables.
  - **Pool**: Configures the virtual machine image for the stage.
  - **Jobs**:
    - **Insert**: Defines a job named "Insert" for inserting documents.
      - **Workspace**: Specifies workspace configurations.
      - **Steps**:
        - `checkout`: Checks out the repository containing utilities (`devopsutils`) which likely contains common scripts or configurations.
        - `script`: Installs `mongosh`, a MongoDB Shell.
        - `checkout`: Checks out the repository containing the MongoDB document insertion script (`cosmodb`).
        - `AzureCLI`: Executes Azure CLI commands to insert documents into the MongoDB collection. The script is provided inline and uses parameters to dynamically fetch the file name for the insertion script.

### Adding MongoDB Connection String to Azure DevOps Library
1. Navigate to your Azure DevOps project.
2. Go to the "Pipelines" section.
3. Click on "Library" in the left sidebar.
4. Click on "Add variable group".
5. Enter the name as `mongo-dev, mongo-uat, mongo-prd` to dynamically create variable groups based on the environment selected.
6. Add a variable named `link` with the value set to your MongoDB connection string for the respective environment.
   - For example:
     ```
     link: your_mongodb_connection_string_here
     ```
7. Save the variable group.

### Modifying Pipeline YAML
- Modify the pipeline YAML to utilize the connection string variable by using the `variables` keyword within the respective stage.
- Update the `mongosh` command in the Azure CLI task to include `--host $(link)` to pass the MongoDB connection string dynamically fetched from the variable group based on the selected environment.
