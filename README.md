# dd-k8s-sandbox
Various DD sandbox scenarios in a k8s environment

## Pre-reqs
- Docker
- kubectl
- minikube
- helm (for deploy Datadog stuff)

Doocker needs to be installed beforehand, but kubectl and minikube can be installed with the following:

### Minikube
```
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-amd64 \
&& chmod +x minikube \
&& mv ./minikube /usr/local/bin \
&& minikube start --extra-config=kubelet.read-only-port=10255
```

### Kubectl
```
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/darwin/amd64/kubectl \
&& chmod +x ./kubectl \
&& mv ./kubectl /usr/local/bin/kubectl
```

### Helm

Via brew
```
brew install helm
```

or by script
```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

## Datadog prep

Once these have been setup, you need to add your API key and APP key to a secret in the cluster. Use the following command to do so:

```
kubectl create secret generic datadog-secret --from-literal api-key=<DATADOG_API_KEY> --from-literal app-key=<DATADOG_APP_KEY>
```
**NOTE: make sure the secrets are named "api-key" and "app-key" in the command. If you change these you will need to update the datadog-agent.yaml file to match. This also applies to the secret name "datadog-secret"**

Replacing <DATADOG_API_KEY> and <DATADOG_APP_KEY> with your actual keys

## Datadog operator

There are 2 ways to put the Datadog agent onto your cluster: via Helm or using the Datadog Operator. The later is recommended as it has a lot of best-practice configuration built in.

Install operator
```
helm repo add datadog https://helm.datadoghq.com
helm install my-datadog-operator datadog/datadog-operator
```

## Datadog agent deploy

Now everything is setup to install the agent on the k8s cluster. This can be done with the following command:

```
kubectl apply -f /path/to/your/datadog-agent.yaml
```

Confirm the agent has been installed by running the following commands:


`kubectl get daemonset` 

Which will give you a result like:

```
NAME            DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
datadog-agent   1         1         1       1            1           <none>          24h
```

and

`kubectl get pod -owide`

Which will give you a result like:

```
NAME                                    READY   STATUS    RESTARTS   AGE   IP           NODE       NOMINATED NODE   READINESS GATES
datadog-agent-wrxtt                     3/3     Running   0          24h   10.244.0.8   minikube   <none>           <none>
datadog-cluster-agent-9f6cbbb55-hh8kq   1/1     Running   0          24h   10.244.0.6   minikube   <none>           <none>
my-datadog-operator-6f896c7886-4j2pk    1/1     Running   0          24h   10.244.0.3   minikube   <none>           <none>
```

**NOTE it can take up to 5 mins (depending on system specs) for all pods to be running, so be patient**

To check the the agent is communicating, go to "Infrastructure" -> "Host Map" and see if the cluster host has appeared.

Example image (the cluster is the minikube on the right):

![Host map](/images/host_map.png)

Once we've reached this point, we can move on to other areas, such as:

APM
LOGS
MONITORS
