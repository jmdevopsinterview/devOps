# Azure DevOps Yaml Pipelines

This task is specifically about creating an Azure DevOps pipeline for a multi-container application which is deployed into Azure Kubernetes Service (AKS).
Ideally it would be great to run through the submission on Azure to help demonstrate your approach, but we realize not everyone will still have free credits on Azure, so there is no requirement to have access to the Azure platform to complete this task. The key goal is to demonstrate that you can effectively use Azure DevOps pipelines documentation to build
a pipeline that can deploy services into AKS and should take no more than a couple of hours. If it is taking longer, it is acceptable to present what you could accomplish in the time-boxed period.

# Task

This task uses a publicly available basic voting app consisting of a front-end web component and a back-end Redis instance. It does not require any programming effort. All source code for the service being built can be found here:

https://github.com/Azure-Samples/azure-voting-app-redis

For components that are referenced in the pipeline (e.g. ACR, AKS), if you will not be demoing your submission on the Azure platform, it is acceptable to assume that these components already exist. Please use names that follow Azure naming standards. For example:

- Resource group: rg-lbg-voteapp-test-uks
- Container registry: cr-lbg-voteapp-test-uks

## Azure DevOps Docs

- Template types & usage
  - https://docs.microsoft.com/en-us/azure/devops/pipelines/process/templates?view=azure-devops
- Build an image
  - https://docs.microsoft.com/en-us/azure/devops/pipelines/ecosystems/containers/build-image?view=azure-devops
- Define variables
  - https://docs.microsoft.com/en-us/azure/devops/pipelines/process/variables?view=azure-devops&tabs=yaml%2Cbatch
- Kubernetes manifest task
  - https://docs.microsoft.com/en-us/azure/devops/pipelines/tasks/deploy/kubernetes-manifest?view=azure-devops
- Publish and download pipeline Artifacts
  - https://docs.microsoft.com/en-us/azure/devops/pipelines/artifacts/pipeline-artifacts?view=azure-devops&tabs=yaml

## Task Deliverables

- 4 files that make up 1 Azure DevOps Build pipeline which will deploy the voting app into an AKS cluster:
  - **File 1**: Main pipeline
    - Pipeline should have sensible split of stages that demonstrate the CI and CD activities
    - Each stage may have 1 or more Jobs and Steps
    - Please use variables opposed to hard coded values, wherever suitable
  - **File 2**: A CI Pipeline Stage
    - This stage of the pipeline should be stored in a separate file and referenced within the main pipeline (i.e. file 1).
  - **File 3**: A CD Pipeline Stage
    - This stage of the pipeline should be stored in a separate file and referenced within the main pipeline (i.e. file 1)
  - **File 4**: Variables
    - Pass in variables to the main pipeline (i.e. file 1)

# Pipeline Content Guidelines

1. CI stage
   1. Pipeline should trigger from the 'main' branch
      1. To simplify, assume that this pipeline is in the Azure-Voting-App-Redis repo
   2. Image build job:
      1. Build a new image. (AzDO: _Docker_)
      2. Image version should use the DevOps '**buildid**' (AzDO: _$(Build.BuildId)_).
      3. Push new image to ACR (AzDO: _Docker_)
      4. Generate a string which will be used later for the manifest name (AzDO: _bash, vso[task.setvariable...._)
         1. The generated string should be assigned as a parameter and retrieved from the next job/stage
         2. Format should be: **voteap_<buildid>_<datetime>**
   3. Generate manifest job:
      1. Rename the manifest file (azure-vote-all-in-one-redis.yaml) using the string parameter from the previous job (AzDO: _CopyFiles_)
         1. Rename format: "**voteapp_<buildid>_<datetime>**.yaml"
      2. Add the deployment manifest file to the build artifacts (AzDO: _publish_)
2. CD stage
   1. Deploy the manifest to AKS job:
      1. Get deployment manifest file from the build artifacts (AzDO: _download_)
      2. Create imagePullSecret (AzDO: _KubernetesManifest_)
      3. Deploy to Kubernetes cluster using imagePullSecret (AzDO: _KubernetesManifest_)
         1. Override the container in the manifest to use the one built earlier
         2. Use imagePullSecret the one built earlier

## Pseudocode Example (of an incomplete pipeline)

```yaml
# File: azure-pipelines.yml
name: <pipeline-name>
trigger:
- master
variables:
- template: variables/vars.yml
stages:
- template: stages/ci.yml
- template: stages/cd.yml
```

```yaml
# File: stages/ci.yml
parameters:
...
stages:
- stage: ...
  jobs:
  - job: ...
    steps:
    ...
  - job: ...
    dependsOn: ...
    variables: ...
    steps:
    ....
```

```yaml
# File: stages/cd.yml
parameters:
...
- stage: ...
  displayName: ...
  dependsOn: ...
  variables: ...
  jobs:
  - deployment: ...
    condition: !!!when source branch is not pull request
    environment: ...
    strategy:
      runOnce:
        deploy:
          steps:
...
```

```yaml
# File: variables/vars.yml
variables:
  resourceGroup: rg-lbg-voteapp-test-uks
...
```

## Show & Tell

##### Walk us through the code detailing all the decisions you made along the way for