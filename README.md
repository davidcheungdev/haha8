#  {{cookiecutter.name}} Backend service

# Essentials steps to get your backend server deployed
- Webserver that listens on a port
- Dockerfile that builds that 
- Creates an ingress, service and deployment in Kubernetes cluster
- CI/CD to build and push the image to deployment

# Things that happened behind the scene
- ECR repository has been created for this repo
- AWS key pair with access to push/read ECR repo
- Cluster name / AWS account id / deployer role name

# Deployment
## Kubernetes
Your application is deployed on your EKS cluster through circleCI, you can see the pod status on kubernetes in your application namespace:
```
kubectl -n {{cookiecutter.name}} get pods
```
### Configuring
You can update the resource limits in the [kubernetes/base/deployment.yml][base-deployment], and control fine-grain customizations based on environment and specific deployments such as Scaling out your production replicas from the [overlays configurations][env-prod]

## Github actions
Your repository comes with a end-to-end CI/CD pipeline, which includes the following steps:
1. Checkout
2. Unit Tests
3. Build Image
4. Upload Image to ECR
4. Deploy image cluster

**Note**: you can add a approval step using Github Environments(Available for Public repos/Github pro), you can configure an environment in the Settings tab then simply add the follow to your ci manifest (`./github/workflows/ci.yml`)
```yml
deploy-production: # or any step you would like to require Explicit approval
  enviroments:
    name: <env-name>
```
### Branch Protection
Your repository comes with a protected branch `master` and you can edit Branch protection in **Branches** tab of Github settings. This ensures code passes tests before getting merged into your default branch.
By default it requires `[lint, unit-test]` to be passing to allow Pull requests to merge.


<!-- Links -->
[base-cronjob]: ./kubernetes/base/cronjob.yml
[base-deployment]: ./kubernetes/base/deployment.yml
[base-deployment-secret]: ./kubernetes/base/deployment.yml#L49-58
[env-prod]: ./kubernetes/overlays/production/deployment.yml
[circleci-details]: ./.circleci/README.md
