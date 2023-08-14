# Deploy TRDL Application using Helm

The chart will deploy the following resources:

- Secret
- Service
- Deployment

## Prerequisites

#### Install tools
- [Helm 3](https://v3.helm.sh/)
- [Kubectl](https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html)
- [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

#### Configure AWS and Cluster Access
1. Create Access token for IAM User
2. Configure aws with the above created access token, this command will create a `.aws` directory with `credentials` and `config`.
   ```
   aws configure
   AWS Access Key ID [None]: <Access id from step1>
   AWS Secret Access Key [None]: <Access token from step1>
   Default region name [None]: <region-name>
   Default output format [None]:
   ```
#### Build Infrastructure
1. Build the infrastructure using terraform from https://github.com/anniemartina/terraform_eks
   
#### Get cluster Access
1. Fetch the kubeconfig from the cluster created using terraform.
   ```
   aws eks update-kubeconfig --name my-trdl-cluster --region eu-north-1
   ```

## Getting Started
Clone the repository
```
git clone git@github.com:anniemartina/helm.git
```

## Helm Configuration and Installation

The following table lists the configuration parameters of the trdl chart and their default values.

| Parameter | Type | Default | Description |
| --------- | ---- | ------- | ----------- |
| `replicaCount` | int | 1 | The number of replicas for the trdl deployment. |
| `ns` | string | trldns | The namespace of the pod. |
| `image.repository` | string | ghcr.io/anniemartina/myhttpserver/trdl | The trdl container image repository. |
| `service.name` | string | trdlsvc | The name of the service. |
| `service.type` | string | LoadBalancer | The type of the service port. |
| `service.port` | int | 80 | Port number of Service port, targetPort, containerPort. |
| `secret.name` | string | trdlsecret | The name of the secret. |
| `secret.type` | string | kubernetes.io/dockerconfigjson | The type of the secret. |

### Installing the chart

Ensure that you are in the chart directory in the repo where `Chart.yaml` is present:
```bash
cd helm
```

To install `trdl` via the Helm chart, use the following to:
- create the trdlns namespace if it doesn't exist
- deploy the trdl chart into the trdlns namespace

```bash
helm install trdl . --namespace trdlns --create-namespace
```

You can override the values for the configuration parameter defined in the table above, either directly in the `values.yaml` file, or via the `--set` switches.

```bash
helm install trdl . --namespace trdlns --create-namespace --set replicaCount="2"
```

### Upgrading the chart

Ensure that you are in the chart directory in the repo where `Chart.yaml` is present:
```bash
cd helm
```

You can modify the `trdl` app by providing new values for the configuration parameter defined in the table above, either directly in the `values.yaml` file, or via the `--set` switches.

```bash
helm upgrade trdl . --namespace trdlns --set replicaCount="1"
```

### Uninstalling the chart

You can uninstall the `trdl` app as follows:

```bash
helm uninstall trdl  --namespace trdlns
```

## Testing and validation
1. Use `kubectl` to get the pods, service, deployment and replicaset
```
kubectl get all -o wide -n trdlns
```
Sample output
```
NAME                        READY   STATUS    RESTARTS   AGE   IP           NODE                                        NOMINATED NODE   READINESS GATES
pod/trdl-59bdd54c4f-28vz7   1/1     Running   0          99s   10.0.2.114   ip-10-0-2-125.eu-north-1.compute.internal   <none>           <none>
pod/trdl-59bdd54c4f-97wq6   1/1     Running   0          99s   10.0.2.62    ip-10-0-2-125.eu-north-1.compute.internal   <none>           <none>

NAME              TYPE           CLUSTER-IP       EXTERNAL-IP                                                               PORT(S)        AGE   SELECTOR
service/trdlsvc   LoadBalancer   172.20.100.251   xxxx.eu-north-1.elb.amazonaws.com   80:32234/TCP   99s   app.kubernetes.io/instance=trdl,app.kubernetes.io/name=trdl

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                                     SELECTOR
deployment.apps/trdl   2/2     2            2           99s   trdl         ghcr.io/anniemartina/myhttpserver/trdl:1   app.kubernetes.io/instance=trdl,app.kubernetes.io/name=trdl

NAME                              DESIRED   CURRENT   READY   AGE   CONTAINERS   IMAGES                                     SELECTOR
replicaset.apps/trdl-59bdd54c4f   2         2         2       99s   trdl         ghcr.io/anniemartina/myhttpserver/trdl:1   app.kubernetes.io/instance=trdl,app.kubernetes.io/name=trdl,pod-template-hash=59bdd54c4f
```
2. nslookup to verify that LoadBalancer resolve
```
nslookup xxxx.eu-north-1.elb.amazonaws.com
```
Sample Output:
```
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
Name:   xxxx.eu-north-1.elb.amazonaws.com
Address: x.x.x.x
Name:   xxxx.eu-north-1.elb.amazonaws.com
Address: x.x.x.x
```
3. Place a GET request to validate the response from the trdl application
```
curl http://xxxx.eu-north-1.elb.amazonaws.com
```
Sample Response
```
42
```
## Troubleshooting
To debug failure in the application pod :
```
kubectl describe pods --namespace trdlns
```

 
