# Install Grafana Using Helm

## Step 10: Deploy Grafana to create Dashboards

### Step 10.1: Install Grafana
```
$ kubectl create namespace grafana
$ helm install stable/grafana \
    --name grafana \
    --namespace grafana \
    --set persistence.storageClassName="gp2" \
    --set adminPassword="EKS!sAWSome" \
    --set datasources."datasources\.yaml".apiVersion=1 \
    --set datasources."datasources\.yaml".datasources[0].name=Prometheus \
    --set datasources."datasources\.yaml".datasources[0].type=prometheus \
    --set datasources."datasources\.yaml".datasources[0].url=http://prometheus-server.prometheus.svc.cluster.local \
    --set datasources."datasources\.yaml".datasources[0].access=proxy \
    --set datasources."datasources\.yaml".datasources[0].isDefault=true \
    --set service.type=LoadBalancer
```

### Step 10.2: Check if Grafana is deployed
```
$ kubectl get all -n grafana
```

### Step 10.3: Get Grafana ELB URL
```
$ export ELB=$(kubectl get svc -n grafana grafana -o jsonpath='{.status.loadBalancer.ingress[0].hostname}')
$ echo "http://$ELB"
```

### Step 10.4: Login using admin and password
```
$ kubectl get secret --namespace grafana grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

### Step 10.5: Create Grafana Dashboards
- Login into Grafana dashboard using credentials supplied during configuration
- You will notice that ‘Install Grafana’ & ‘create your first data source’ are already completed. We will import community created dashboard for this tutorial
- Click ‘+’ button on left panel and select ‘Import’
- Enter 3131 dashboard id under Grafana.com Dashboard & click ‘Load’.
- Leave the defaults, select ‘Prometheus’ as the endpoint under prometheus data sources drop down, click ‘Import’.
- This will show monitoring dashboard for all cluster nodes
- For creating dashboard to monitor all pods, repeat same process as above and enter 3146 for dashboard id

### (Optional) Clean up
```
$ helm delete grafana
$ helm del --purge grafana
```
