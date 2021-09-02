# build-push-deploy-cloud-run
Build a docker image, Upload it to Google cloud image registry and deploy a new Cloud Run revision

## Table of contents
- [Prerequisites](#prerequisites)
- [Usage](#usage)
- [Inputs](#inputs)
- [Outputs](#outputs)

# Prerequisites

This action requires:

 1. A Google Cloud service account with the following roles:

    - Cloud Run Admin
    - Storage Admin
    - Service Account User

    ![alt text](roles.png?raw=true "Service Account Roles")

2. Enable the Cloud Run API

# Usage
An Example workflow of Compiling, Building, Pushing and Releasing a Typescipt Project

```yaml
    steps:
    - name: Checkout Code
      uses: actions/checkout@v2

    - name: Setup Node
      uses: actions/setup-node@v2
      with:
        node-version: '16'

    - name: Compile Typescript
      run: npx tsc

    - name: Deploy to Cloud Run
      uses: ride-app/deploy-cloud-run@v1
      with:
        project-id: demo-project
        image-name: hello-world
        service-account: ${{ secrets.GCP_SA_KEY }}
```

# Inputs

| Name          | Requirement | Default | Description |
| ------------- | ----------- | ------- | ----------- |
| `project-id`| Required | | ID of the Google Cloud project |
| `image-name`| Required | | Name of the container image to deploy (Example: `gcr.io/project-id/image-name:latest`). |
| `service-account`| Required | | Service account key to use for authentication. This should be the JSON formatted private key which can be exported from the Cloud Console. The value can be raw or base64-encoded.  |
| `region`| _optional_ | `us-central1` | Region in which the resource can be found. |
| `dockerfile-path`| _optional_ | | Path of the dockerfile to use to build the container. |
| `build-args`| _optional_ | | Build Arguments to pass to docker during build. This is only required if your dockerfile uses `ARG` to take build arguments. |
| `env_vars`| _optional_ | | List of key-value pairs to set as environment variables in the format: `KEY1=VALUE1,KEY2=VALUE2`. **All existing environment variables will be retained**. |
| `secrets`| _optional_ | | List of key-value pairs to set as either environment variables or mounted volumes in the format: `KEY1=secret-key-1:latest,/secrets/api/key=secret-key-2:latest`. The secrets will be fetched from the Secret Manager. **All existing environment secrets or volumes will be retained**. |
| `tag` | _optional_ | | Traffic tag to assign to the newly created revision. |

# Outputs

- `url`: The URL of the deployed Cloud Run service revision.
