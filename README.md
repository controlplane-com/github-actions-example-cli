# Control Plane - GitHub Actions Example Using the CLI

This example demonstrates building and deploying an app to Control Plane using the CLI (cpln) as part of a GitHub Action. 

The example is a Node.js app that displays the environment variables and start-up arguments.

This example is provided as a starting point and your own unique delivery and/or deployment requirements will dictate the steps needed in your situation.

## Control Plane Authentication Set Up 

The Control Plane CLI require a `Service Account` with the proper permissions to perform actions against the Control Plane API. 

1. Follow the Control Plane documentation to create a Service Account and create a key. Take a note of the key. It will be used in the next section.
2. Add the Service Account to the `superusers` group. Once the GitHub Action executes as expected, a policy can be created with a limited set of permissions and the Service Account can be removed from the `superusers` group.
   
## Example Set Up

When triggered, the GitHub action will execute the steps defined in the workflow file located at `.github/workflow/deploy-to-control-plane.yml`. The example will containerize and push the application to the org's private image repository and create/update a GVC and workload hosted at Control Plane. 

**Perform the following steps to set up the example:**

1. Fork the example into your own workspace.

2. The following variables are required and must be added as GitHub repository secrets.

Browse to the Secrets page by clicking `Settings` (top menu bar), then `Secrets` (left menu bar).

- `CPLN_ORG`: Control Plane org.
- `CPLN_GVC`: The name of the GVC.
- `CPLN_WORKLOAD`: The name of the workload.
- `CPLN_TOKEN`: Service Account Key.
- `CPLN_IMAGE_NAME`: The name of the image that will be deployed. The workflow will append the short SHA of the commit as the image tag when pushing the image to the org's private image repository.

3. Review the `.github/workflow/deploy-to-control-plane.yml` file:
    - The workflow can be updated to be triggered on specific branches and actions (pushes, pull requests, etc.). The example is set to trigger on a push or pull request to the `main` branch on lines 9-11 (currently commented out).
    - The `sed` command is used to substitute the `ORG_NAME`, `GVC_NAME`, `WORKLOAD_NAME` and `IMAGE_NAME_TAG` tokens inside the YAML files in the `/cpln` directory on lines 55-58.

4.  The Control Plane YAML files are located in the `/cpln` directory. No changes are required to execute the example.
    - The `cpln-gvc.yaml` file defines the GVC to be created/updated.
    - The `cpln-workload.yaml` file defines the workload to be created/updated. 

## Running the App

After the GitHub Action has successfully deployed the application, it can be tested by following these steps:

1. Browse to the Control Plane Console.
2. Select the GVC that was set in the `/cpln/cpln-gvc.yaml` file.
3. Select the workload that was set in the `/cpln/cpln-workload.yaml` file.
4. Click the `Open` button. The app will open in a new tab. The container's environment variables and start up arguments will be displayed.

## Notes

- The `cpln apply` command creates and updates the resources defined within the YAML file. If the name of a resource is changed, `cpln apply` will create a new resource. Any orphaned resources will need to be manually deleted.

- The Control Plane CLI commands use the `CPLN_ORG` and `CPLN_TOKEN` environment variables when needed. There is no need to add the --org or --token flags when executing CLI commands.

- The GVC definition must exists in its own YAML file. The `cpln apply` command executing the file that contains the GVC definition must be executed before any child definition YAML files (workloads, identities, etc.) are executed.

## Helper Links

GitHub

- <a href="https://docs.github.com/en/actions" target="_blank">GitHub Actions Docs</a>
