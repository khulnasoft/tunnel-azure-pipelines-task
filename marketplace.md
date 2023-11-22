# tunnel

An Azure DevOps Pipelines Task for [Tunnel](https://github.com/khulnasoft/tunnel), with an integrated UI.

![Screenshot showing the tunnel extension in the Azure Devops UI](screenshot.png)

## Installation

1. Install the Tunnel task in your Azure DevOps organization (hit the `Get it free` button above).

2. Add the task to your `azure-pipelines.yml` in a project where you'd like to run tunnel:

```yaml
- task: tunnel@1
```

## Configuration

You can supply several inputs to customise the task.

| Input        | Description                                                                                                                          |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------|
| `version`    | The version of Tunnel to use. Currently defaults to `latest`.                                                                         |
| `docker`     | Run Tunnel using the khulnasoft/tunnel docker image. Alternatively the Tunnel binary will be run natively. Defaults to `true`.             |
| `loginDockerConfig` | Set this to true if the `Docker login` task is used to access private repositories. Defaults to `false`.                      |
| `debug`      | Enable debug logging in the build output.                                                                                            |
| `path`       | The path to scan relative to the root of the repository being scanned, if an `fs` scan is required. Cannot be set if `image` is set. |
| `severities` | The severities (`CRITICAL,HIGH,MEDIUM,LOW,UNKNOWN`) to include in the scan (comma sperated). Defaults to `CRITICAL,HIGH,MEDIUM,LOW,UNKNOWN`. |
| `ignoreUnfixed` | When set to `true` all unfixed vulnerabilities will be skipped. Defaults to `false`.                                               |
| `image`      | The image to scan if an `image` scan is required. Cannot be set if `path` is set.                                                    |
| `exitCode`   | The exit-code to use when Tunnel detects issues. Set to `0` to prevent the build failing when Tunnel finds issues. Defaults to `1`.    |
| `aquaKey`    | The Khulnasoft API Key to use to link scan results to your Khulnasoft Security account _(not required)_.                                         |
| `aquaSecret` | The Khulnasoft API Secret to use to link scan results to your Khulnasoft Security account _(not required)_.                                      |
| `options`    | Additional flags to pass to tunnel. Example: `--timeout 10m0s` _(not required)_.                                                      |

### Example of scanning multiple targets

```yaml
trigger:
- main

pool:
  vmImage: ubuntu-latest

jobs:
- job: Scan the local project
  steps:
  - task: tunnel@1
    inputs:
      path: .
- job: Scan the ubuntu image
  steps:
  - task: tunnel@1
    inputs:
      image: ubuntu
```

## Scanning Images in Private Registries

You can scan images in private registries by using the `image` input after completing a `docker login`. For example:

```yaml
steps:
- task: Docker@2
  displayName: Login to ACR
  inputs:
    command: login
    containerRegistry: dockerRegistryServiceConnection1
- task: tunnel@1
  inputs:
    image: my.registry/org/my-image:latest
```
