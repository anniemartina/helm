# Deploy using Helm

The `trdl` Helm chart can be used to deploy the `trdl` application. The chart will deploy the following resources:

- Secret
- Service
- Deployment

## Prerequisites

- [Helm 3](https://v3.helm.sh/)

## Configuration and installation

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
helm uninstall trdl  --namespace trdlns```
