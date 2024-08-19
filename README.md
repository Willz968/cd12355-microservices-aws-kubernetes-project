# Coworking Space Service Extension

The Coworking Space Service is a set of APIs that enables users to request one-time tokens and administrators to authorize access to a coworking space. This service follows a microservice pattern and the APIs are split into distinct services that can be deployed and managed independently of one another.

This project provides Infrastructure as Code in the form of YAML templates, for deploying the following distinct services to AWS EKS:
- Postgresql Database
- Coworking Application

Kubectl is used to post the manifests to the connected Kubernetes API server, configured via `kubeconfig`.

# Deployment process

## Apply Kubernetes Manifest files

Kubernetes Manifest files (YAML templates) may be applied from the `deployment` directory with the following command:

`kubectl apply -f deployment\.`

An overview of the purpose of each template is as follows:

| Template | Description |
| --- | --- |
| configmap.yaml | Contains supplied configuration settings: Database Host, Port, Username, Name |
| coworking.yaml | Configures the Coworking Deployment and its LoadBalancer. The application image references the application image hosted in AWS ECR |
| postgresql-deployment.yaml | Creates the Postgresql Databse, populates with configuration as provided in the configmap.yaml and secret.yaml |
| postgresql-service.yaml | Creates the Postgresql Service to be used for accessing the Postgresql Database |
| pv.yaml | Contains the PersistentVolume spec |
| pvc.yaml | Contains the PersistentVolumeClaim spec |
| secret.yaml | Contains the supplied Base64 encoded database Password |

## Verify Resources are created

Once the Kubernetes Manifests have been deployed, you may verify that the Pods and Services are running with the following commands:

`kubectl get pods`

`kubectl get svc`

You should expect to see the following Pods and Services running.

| Resource type | Resource name |
| --- | --- |
| Pod | coworking |
| Pod | postgresql |
| Service | coworking |
| Service | kubernetes |
| Service | postgresql-service |

## Accessing Reports

Run a `kubectl get svc` and take note of the URL listed under the EXTERNAL-IP of the Coworking LoadBalancer service.

The following commands may be used to access the Coworking reports. Make sure to substitute the LoadBalancer URL in place of `<external-ip>`:

`curl <external-ip>:5153/api/reports/daily_usage`

`curl <external-ip>:5153/api/reports/user_visits`

# Updating the Coworking Space Service

Changes to the Kubernetes deployment should be made to the Kubernetes Manifest files in the Deployment folder, to ensure that changes are reflected in a declarative manner.

Changes to the application should be made in the Analytics folder. Please note that any changes to the application will require a new Docker image to be created for publication to the ECR.
This can either be manually pushed to the ECR, or a CodeBuild Build Project may be configured to automatically push any new images to the ECR when a Pull Request is merged to the connected GitHub repository.

If manually building a new image, this may be achieved by running the commands listed under `View push commands` in your ECR repository.

If building a new image via pipeline using a CodeBuild Build Project, then this is controlled by `analytics\buildspec.yaml`.