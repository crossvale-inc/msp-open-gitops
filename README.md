
# Crossvale Kubernetes Manager

This repository contains one stack of applications along with their configuration that can be deployed on any Kubernetes cluster, such as EKS, AKS and OpenShift. This application stack was carefully selected to provide most of the functionality needed in an enterprise environment. 

This stack is basically formed by 4 basic components: 

- Cert-Manager: The purpose of this component is to provide certificate management as there is no such component integrated in EKS or similar clusters. Thanks to the existence of certificates in the cluster it is possible to have secure communications or even secure access to the applications themselves. The component chosen for this purpose is Cert-Manager and the way to deploy it is through a Helm chart that comes from its official repository: https://artifacthub.io/packages/helm/cert-manager/cert-manager.

    Here is the official documentation of Cert-Manager if anyone wants to know more about the component: https://cert-manager.io/docs/

	
- Kubernetes-Dashboard: most Kubernetes clusters don't have a console or the one they have has little usability. For this reason the Kubernetes-Dashboard is deployed automatically with this stack. The deployment method is through the Helm chart of the official Kubernetes GitHub: https://github.com/kubernetes/dashboard

    Here is the official Kubernetes console documentation from the official Kubernetes website if anyone wants to learn more about its configuration and deployment: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/


- EFK stack: Kubernetes clusters by default do not have a logging solution. This means that there is no possibility to work on the logs of the applications in the cluster. This is very useful for troubleshooting. It has been decided to deploy ElasticSearch, kibana and MetricBeat through the helm chart of the official GitHub repository: https://github.com/elastic/helm-charts/tree/main/elasticsearch, https://github.com/elastic/helm-charts/tree/main/kibana, https://github.com/elastic/helm-charts/tree/main/metricbeat. 

    Here is the official ElasticSearch, Kibana and MetricBeat documentation from the official ElasticSearch website if anyone wants to learn more about its configuration and deployment: https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html


- Prometheus-Stack: As with the previous component, by default, in Kubernetes clusters there is no solution that allow the users to save and interact with the metrics giving a monitoring solution. In this Open-Crossvale version it has been decided to deploy the Prometheus-Stack through its helm chart because when it is configured correctly it deploys an entire monitoring solution based on Prometheus, Grafana and Alertmanager. https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack.

    Here is the official prometheus documentation from the official Prometheus website if anyone wants to learn more about its configuration and deployment: https://prometheus.io/docs/introduction/overview/




## Prerequisites

The only prerequisites needed to be able to deploy the stack are: 

- Enough CPU and memory capacity in the cluster
- The possibility to create RWO volumes.
- ArgoCD deployed in the cluster in a namespace called argocd. In case this is not the situation, the objects of the repo will have to be modified to point to the namespace where argocd is deployed in the cluster (Question already answered on FAQ section).
- ArgoCD needs to have enough permissions on the clusters to be able to create, update, delete objects in new and old namespaces.


## Deployment Method

Taking into account the prerequisites, deploying the entire stack is very easy. Just go to the repository inside the directory called apps-of-apps and apply the apps-of-apps.yaml file in the cluster.

```bash
  cd apps-of-apps
  kubectl apply -f apps-of-apps.yaml
```

Once this is done, a number of applications will be deployed in the ArgoCD that will in turn deploy each of the necessary components in a completely autonomous and automatic way. 

When all the applications are health and synced it can be confirmed that the stack was successfully deployed.


## Working with stack applications

Once all the applications in ArgoCD are running in order to access the components and work with them is needed to execute the following commands:

- To get the Grafana admin user and password run this command: 

```bash
echo -n "admin-username: " &&  kubectl get secret -n prometheus-stack prometheus-stack-grafana -o jsonpath='{.data.admin-user}' | base64 -d && echo " " && echo -n "admin-password: " &&  kubectl get secret -n prometheus-stack prometheus-stack-grafana -o jsonpath='{.data.admin-password}' | base64 -d
```
- To access Grafana run this command, access through localhost from the browser and introduce the credentials obtained from the previous command. http://localhost:3000

```bash
kubectl port-forward -n prometheus-stack svc/prometheus-stack-grafana 3000:80
```


- To access Alertamanger run this command and access through localhost from the browser. http://localhost:9093

```bash
kubectl port-forward -n prometheus-stack svc/alertmanager-operated 9093
```

- To access Prometheus run this command and access it through localhost from the browser http://localhost:9090

```bash
kubectl port-forward -n prometheus-stack svc/prometheus-stack-kube-prom-prometheus 9090
```

- To get the Elasticsearch user and password run this command: 

```bash
echo -n "admin-username: " &&  kubectl get secret -n elasticsearch elasticsearch-master-credentials  -o jsonpath='{.data.username}' | base64 -d && echo " " && echo -n "admin-password: " &&  kubectl get secret -n elasticsearch elasticsearch-master-credentials -o jsonpath='{.data.password}' | base64 -d
```

- To access Kibana run this command, access through localhost from the browser and introduce the credentials obtained from the previous command. http://localhost:5601

```bash
kubectl port-forward -n elasticsearch svc/kibana-kibana 5601
```

- And to access the Kubernetes-Dashboard run this command and access through localhost from the browser. https://localhost:3000/

```bash
kubectl port-forward -n kubernetes-dashboard svc/kubernetes-dashboard 3000:443
```

# Additional notes

The current stack configuration was tested to work in all types of Kubernetes clusters as long as the above prerequisites are met in relatively small environments. 
At the same time, the access to the applications will only work if there is direct access to the cluster from within the network. 

It should be noted that the configuration of each component is done in the values files of each application that contains the word values in its name inside each directory of the repository.

In case someone needs to modify the components due to individual features please go to the FAQ section.
## FAQ

### What if I need to configure the components differently?  

 In this case I recommend you to clone the repository in your own repository and make modifications in the applications so that they point to your new repo and at the same time modify the values you need for each helm chart of each application.

Another option is to contact Crossvale as we are currently also supporting the premium version of this stack where apart from maintaining, deploying and supporting a better stack we also take care of certain individual requests.

In case you want to contact Crossvale please send an email to sales@crossvale.com or also you can visit Crossvale webpage https://crossvale.com/




## Authors

- [@Javier Lagos Chanclón](https://github.com/JavierLagosChanclon)
- [@Diego Perez Hidalgo](https://github.com/DiegoBskt)
- [@Carlos Santiago Gordillo](https://github.com/csantiagoo)
- [@Dario Pazos Pose](https://github.com/DarioPazos)
- [@Arturo Velez Duque](https://github.com/avelezxv)

