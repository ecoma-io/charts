# Cloudflared

A tunneling daemon that proxies traffic from the Cloudflare network to your origins. This daemon sits between Cloudflare network and your origin (e.g. a webserver). Cloudflare attracts client requests and sends them to you via this daemon, without requiring you to poke holes on your firewall --- your origin can remain as closed as possible. Extensive documentation can be found in the [Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps) section of the Cloudflare Docs.

## Introduction

Cloudflare Tunnel provides you with a secure way to connect your resources to Cloudflare without a publicly routable IP address. With Tunnel, you do not send traffic to an external IP — instead, a lightweight daemon in your infrastructure (cloudflared) creates outbound-only connections to Cloudflare’s edge. Cloudflare Tunnel can connect HTTP web servers, SSH servers, remote desktops, and other protocols safely to Cloudflare. This way, your origins can serve traffic through Cloudflare without being vulnerable to attacks that bypass Cloudflare.

## Prerequisites

- Kubernetes 1.23+
- Helm 3.2.0+
- Argo Tunnel ID generated

## Installing the Chart

To install the chart with the release name `cloudflare-tunnel`:

```console
helm repo add ecomma https://ecomma-io.github.io/helm-charts
helm repo update
helm install cloudflare-tunnel Ecomadev/cloudflared
```

The command deploys cloudflared on the Kubernetes cluster in the default configuration. The [Parameters](#parameters) section lists the parameters that can be configured during installation.

> **Tip**: List all releases using `helm list`

## Uninstalling the Chart

To uninstall/delete the `cloudflare-tunnel` deployment:

```console
helm delete cloudflare-tunnel
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Parameters

### Image parameters

| Name                    | Description                                   | Value                    |
| ----------------------- | --------------------------------------------- | ------------------------ |
| `image.repository`      | The Docker repository to pull the image from. | `cloudflare/cloudflared` |
| `image.tag`             | The image tag to use.                         | `2025.2.1`               |
| `image.imagePullPolicy` | The logic of image pulling.                   | `IfNotPresent`           |

### Deployment parameters

| Name                        | Description                                                                                                                                   | Value   |
| --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- | ------- |
| `replicaCount`              | The number of replicas to deploy.                                                                                                             | `1`     |
| `affinity`                  | If specified, the pod's scheduling constraints. Affinity is a group of affinity scheduling rules.                                             | `{}`    |
| `tolerations`               | Tolerates any taint that matches the triple `<key,value,effect>` using the matching operator `<operator>`.                                    | `[]`    |
| `topologySpreadConstraints` | Control how pods are spread across your cluster among failure-domains such as regions, zones, nodes, and other user-defined topology domains. | `[]`    |
| `resources`                 | If specified, it sets the resource requests and limits for the pod.                                                                           | `{}`    |
| `logLevel`                  | The log level to use for the tunnel.                                                                                                          | `info`  |
| `priorityClassName`         | The priority class name to use for the tunnel.                                                                                                | `""`    |
| `metrics.enabled`           | Enable metrics for prometheus monitoring. The crd monitoring.coreos.com/v1 must be already installed on the target.                           | `false` |
| `metrics.port`              | The port to use for metrics.                                                                                                                  | `""`    |
| `tunnel.enabled`           | Whether to enable Managed (CF Zero Trust Dashboard) tunnel configuration. Cannot coexist with the local one.                                  | `true`  |
| `tunnel.token`             | The connector token provided at the end of the CF Zero Trust tunnel creation.                                                                 | `""`    |
| `tunnel.existingSecret`    | The name of the existing secret containing the token. The secret key must be set to 'cf-tunnel-token'.                                        | `""`    |
| `tunnel.protocol`          | Specifies the protocol used to establish a connection between cloudflared and the Cloudflare global network. (auto, http2, quic)              | `""`    |
| `tunnel.retries`           | Specifies the maximum number of retries for connection/protocol errors.                                                                       | `""`    |


## Configuration and installation details

### Managed Setup

- Go to the Cloudflare dashboard of your account and enable Zero Trust. Once there, in Access -> Tunnels you can create a tunnel and get the connector token.

#### Setting up the Argo Tunnel ingress options with Traefik

To use the tunnel with Traefik, you need to configure the ingress settings. As cloudflared works with CNAMEs, you want to set a wildcard hostname for the service, and set the origin request setting to be the root domain that you are configuring this for. Also, you need to point the service to the secure port (443) of the Traefik load balancer service. Here is an example configuration:


## License

Copyright &copy; 2024 Ecoma

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
