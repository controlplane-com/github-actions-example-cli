# Control Plane - GitHub Actions Example Using the CLI

This example demonstrates how to build and deploy an application to Control Plane using the Control Plane CLI as part of a CI/CD pipeline. 

The sample application is a Node.js web application that will display the environment variables that exist in the running container and the arguments that were used when executing the container. A sample Dockerfile is included to assist in the building of the container.

This example is provided as a starting point and your own unique delivery and/or deployment requirements will dictate the steps needed in your situation.

## Control Plane Authentication Set Up 

The Control Plane CLI require a `Service Account` with the proper permissions to perform actions against the Control Plane API. 

1. Follow the Control Plane documentation to create a Service Account and create a key. Take a note of the key. It will be used in the next section.
2. Add the Service Account to the `superusers` group. Once the pipeline executes as expected, a policy can be created with a limited set of permissions and the Service Account can be removed from the `superusers` group.
   
## GitHub Set Up

The example require the following variables be added as a secret:

- `CPLN_ORG`: Control Plane org.
- `CPLN_TOKEN`: Service Account Key.
- `CPLN_IMAGE_NAME`: The name of the image that will be deployed. The workflow will append the short SHA of the commit when pushing the image to the org's private image repository.

Browse to the Secrets page by clicking `Settings` (top menu bar), then `Secrets` (left menu bar).

## Example Set Up

When triggered, the GitHub action will execute the steps defined in the workflow file located at `.github/workflow/deploy-to-control-plane.yml`. The example will containerize and push the application to the org's private image repository and create/update a GVC and workload hosted at Control Plane. 

**Perform the following steps to set up the example:**

1. Fork the example into your own workspace.

2. Review the `.github/workflow/deploy-to-control-plane.yml` file:
    - The workflow can be updated to be triggered on specific branches and actions (pushes, pull requests, etc.). The example is set to trigger on a push or pull request to the `main` branch. 
    - The `sed` command is used to substitute the `ORG_NAME` and `IMAGE_NAME_TAG` tokens within the YAML files in the `/cpln` directory.
    - The `yq` command is used to extract the GVC name from the `/cpln/cpln-gvc.yaml` file. It is used by the `--gvc` flag when executing the `cpln apply` command for the `cpln-workload.yaml` file.

3. Review the two files in the `/cpln` directory:
    - The `cpln-gvc.yaml` file defines the GVC to be created/updated. Update the GVC name.
    - The `cpln-workload.yaml` file defines the workload to be created/updated. Update the workload name.

## Running Example Application

After the pipeline has successfully deployed the application, it can be tested by following these steps:

1. Browse to the Control Plane Console.
2. Select the GVC that was set in the `/cpln/cpln-gvc.yaml` file.
3. Select the workload that was set in the `/cpln/cpln-workload.yaml` file.
4. Click the `Open` button. The example application will open in a new tab. It will display the environment variables that exist in the running container and the arguments that were used when executing the container.

## Notes

- `cpln apply` creates and updates the resources defined within the YAML file. If the name of a resource is changed, `cpln apply` will create a new resource. Any orphaned resources will need to be manually deleted.

- The Control Plane CLI commands use the `CPLN_ORG` and `CPLN_TOKEN` environment variables when needed. There is no need to add the --org or --token flags when executing CLI commands.

- The GVC definition must exists in its own YAML file. The `cpln apply` command using the file must be executed before any child (workloads, identities, etc.) definition YAML files are executed.

## Helper Links

GitHub

- <a href="https://docs.github.com/en/actions" target="_blank">GitHub Actions Docs</a>
