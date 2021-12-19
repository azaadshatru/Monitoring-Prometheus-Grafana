# Monitoring-Prometheus-Grafana

## Deployment of Prometheus, Alert Manager, Grafana and Node Exporter on Kubernetes Cluster for SAS Viya 4 Monitoring

### Install Prometheus on Kubernetes Cluster

```
# mkdir ~/monitoring
# git clone https://github.com/bibinwilson/kubernetes-prometheus
# cd ~/monitoring/kubernetes-prometheus
# kubectl create namespace monitoring
# kubectl create -f clusterRole.yaml
# kubectl create -f config-map.yaml
# kubectl create  -f prometheus-deployment.yaml 
# kubectl get pod -n monitoring
# kubectl get deployments --namespace=monitoring
# kubectl get pods --namespace=monitoring
NAME                                     READY   STATUS    RESTARTS   AGE
prometheus-deployment-54686956bd-hdsnx   1/1     Running   0          2m59s
# kubectl port-forward prometheus-deployment-54686956bd-hdsnx 8080:9090 -n monitoring
# kubectl create -f prometheus-service.yaml --namespace=monitoring

URL: http://k8s-master-1:30000/
http://192.168.1.1:30000
Internal Prometheus Service: http://10.244.73.86:9090
```

### Install Alert Manager 

#### You should have a working Prometheus setup up and running

```
# cd ~/monitoring
# git clone https://github.com/bibinwilson/kubernetes-alert-manager.git
# cd ~/monitoring/kubernetes-alert-manager/
# kubectl create -f AlertManagerConfigmap.yaml
# kubectl create -f AlertTemplateConfigMap.yaml
# kubectl create -f Deployment.yaml
# kubectl get pods --namespace=monitoring
NAME                                     READY   STATUS    RESTARTS   AGE
alertmanager-8fb4ccb87-bzrwv             1/1     Running   0          39s
prometheus-deployment-54686956bd-hdsnx   1/1     Running   0          30m

URL: http://k8s-master-1:31000/#/alerts
```

### Install Grafana

#### You should have a working Prometheus setup up and running

```
# cd ~/monitoring
# git clone https://github.com/bibinwilson/kubernetes-grafana.git
# cd ~/monitoring/kubernetes-grafana/
# vi grafana-datasource-config.yaml

#### Change the URL to actual Prometheus URL

"url": "http://k8s-master-1:30000/"

# kubectl create -f grafana-datasource-config.yaml
# kubectl create -f deployment.yaml
# kubectl create -f service.yaml
# kubectl get pods --namespace=monitoring
NAME                                     READY   STATUS    RESTARTS   AGE
alertmanager-8fb4ccb87-bzrwv             1/1     Running   0          65m
grafana-548fdc7598-ngjps                 1/1     Running   0          2m13s
prometheus-deployment-54686956bd-hdsnx   1/1     Running   0          95m
# kubectl get po -n monitoring
NAME                                     READY   STATUS    RESTARTS   AGE
alertmanager-8fb4ccb87-bzrwv             1/1     Running   0          67m
grafana-548fdc7598-ngjps                 1/1     Running   0          3m37s
prometheus-deployment-54686956bd-hdsnx   1/1     Running   0          96m

URL: http://k8s-master-1:32000/ (admin/admin) (You will be promted for change password)
http://192.168.1.1:32000
```

### Install Prometheus Node Exporter

#### You should have a working Prometheus setup up and running

```
# cd ~/monitoring
# git clone https://github.com/bibinwilson/kubernetes-node-exporter
# cd ~/monitoring/kubernetes-node-exporter
# kubectl create -f daemonset.yaml
# kubectl get daemonset -n monitoring
NAME            DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
node-exporter   3         3         0       3            0           <none>          7s
# kubectl create -f service.yaml
# kubectl get service -n monitoring
NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
alertmanager         NodePort    10.99.38.176    <none>        9093:31000/TCP   83m
grafana              NodePort    10.109.158.32   <none>        3000:32000/TCP   18m
node-exporter        ClusterIP   10.100.152.11   <none>        9100/TCP         118s
prometheus-service   NodePort    10.109.155.37   <none>        8080:30000/TCP   103m
# kubectl get endpoints -n monitoring 
NAME                 ENDPOINTS                                                AGE
alertmanager         10.244.73.119:9093                                       81m
grafana              10.244.73.73:3000                                        16m
node-exporter        10.244.69.47:9100,10.244.73.101:9100,10.244.73.12:9100   15s
prometheus-service   10.244.73.86:9090                                        101m

```
#### Some Screenshots
![Grafana](./Grafana_1.png)

![Grafana](./Grafana_2.png)
