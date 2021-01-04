## Amazon ECS "Set Container Property in Task Definition" Action for GitHub Actions

Updates a given property of a container identified by its name in an Amazon ECS task definition JSON file, creating a new task definition file.

**Table of Contents**

<!-- toc -->

- [Usage](#usage)
- [License Summary](#license-summary)
- [Security Disclosures](#security-disclosures)

<!-- tocstop -->

## Usage

To update the `command` property to `'["npm", "run", "serve"]'` in the container
named `myapp` in the task definition file, and then deploy the edited task definition file to ECS:

```yaml
- name: Render Amazon ECS task definition
  id: render-web-container
  uses: clarifydata/amazon-ecs-task-definition-set-container-property@master
  with:
    task-definition: task-definition.json
    container-name: myapp
    property-name: command
    value: '["npm", "run", "serve"]'

- name: Deploy to Amazon ECS service
  uses: aws-actions/amazon-ecs-deploy-task-definition@v1
  with:
    task-definition: ${{ steps.render-web-container.outputs.task-definition }}
    service: my-service
    cluster: my-cluster
```

If your task definition file holds multiple containers in the `containerDefinitions`
section which require updated container names, chain multiple executions of this action
together using the output value from the first action for the `task-definition`
input of the second:

```yaml
- name: Render Amazon ECS task definition for first container
  id: render-myapp-container
  uses: clarifydata/amazon-ecs-task-definition-set-container-property@master
  with:
    task-definition: task-definition.json
    container-name: myapp
    property-name: command
    value: '["npm", "run", "serve"]'

- name: Modify Amazon ECS task definition with second container
  id: render-app-container
  uses: clarifydata/amazon-ecs-task-definition-set-container-property@master
  with:
    task-definition: ${{ steps.render-myapp-container.outputs.task-definition }}
    container-name: postgres
    property-name: essential
    value: "true"

- name: Deploy to Amazon ECS service
  uses: aws-actions/amazon-ecs-deploy-task-definition@v1
  with:
    task-definition: ${{ steps.render-app-container.outputs.task-definition }}
    service: my-service
    cluster: my-cluster
```

See [action.yml](action.yml) for the full documentation for this action's inputs and outputs.

## License Summary

This code is made available under the MIT license.

## Security Disclosures

If you would like to report a potential security issue in this project, please do not create a GitHub issue. Instead, please follow the instructions [here](https://aws.amazon.com/security/vulnerability-reporting/) or [email AWS security directly](mailto:aws-security@amazon.com).
