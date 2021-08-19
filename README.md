# Control Plane - GitHub Actions Example Using the CLI

This example demonstrates how to build and deploy your application to Control Plane using the CLI as part of a CI/CD pipeline. 

It can be used as a template for your application.

## Script Overview

1. Check out code.
2. Set commit short SHA as a variable.
3. Install dependencies:
    - Control Plane CLI
    - yq (Optional, used to extract GVC name for use by the `cpln apply` command)
4. Authenticate, build and push application to the org's private image repository. The short SHA is used as the image tag.
5. Substitute tokens in the resource YAML files.
6. Deploy to Control Plane using the `cpln apply` command.

## Resources to Create/Update

- Create a Control Plane Service Account and add a key
    - Add the Service Account to the `superusers` group. Once the workflow executes as expected, a policy can be created with a limited set of permissions and the Service Account can be removed from the superusers group.
    - Create and save a key. It will be used in the next step.

- Add the following variables as repository secrets (settings -> secrets):
    - `CPLN_ORG`: Org name 
    - `CPLN_TOKEN`: Generated Service Account Key
    - `CPLN_IMAGE_NAME`: The name of the image that will be deployed. The action will append the short SHA of the commit when pushing the image to the org's private image repository.

- Review the `.github/workflows/deploy-to-control-plane.yml` file
    - Update the `on` block with the branches / dispatch rules this action will trigger on. The `workflow_dispatch` element will allow the action to be manually triggered.
    - Step starting on line 49: Update any substitutions needed within the YAML files. The example updates the org name in the `cpln-gvc.yaml` file and the image name and tag in the `cpln-workload.yml` file.
    - Step starting on line 56: Add any additional `cpln apply` commands.  After the `cpln apply` for the GVC is executed, the subsequent `cpln apply` commands can contain multiple resources separated with the document start marker `---` in the YAML file.

- Review the resources in the YAML files in the `/cpln` directory and updated as necessary. 

## Notes

- Using `cpln apply` creates and updates the resources defined within the YAML files. If the name of a resource is changed, `cpln apply` will create a new resource. Any orphaned resources will need to be deleted manually.

- The Control Plane CLI commands will use the `CPLN_ORG` and `CPLN_TOKEN` environment variables when needed. There is no need to add the --org or --token flags.

## Helper Links

- <a href="https://docs.github.com/en/actions" target="_blank">GitHub Actions Docs</a>
