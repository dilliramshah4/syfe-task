# WordPress Kubernetes Infrastructure - Syfe Intern Assignment

## Assignment Objectives  COMPLETED

### #1 Production WordPress on Kubernetes
-  PersistentVolumeClaims and PersistentVolumes
-  Custom Dockerfiles (WordPress, MySQL, Nginx with OpenResty+Lua)
-  Nginx proxy pass to WordPress
-  Helm chart deployment: `helm install my-release charts/wordpress/`
-  Clean deployment: `helm delete my-release`

### #2 Monitoring and Alerting
-  Prometheus/Grafana stack deployed
-  Pod CPU utilization monitoring
-  Nginx Total Request Count via `/nginx_status`
-  Nginx 5xx error monitoring
-  Complete metrics documentation

## Quick Deploy

```bash
# Build images
docker build -t nginx-openresty:latest docker/nginx/
docker build -t wordpress-custom:latest docker/wordpress/
docker build -t mysql-custom:latest docker/mysql/

# Import to k3d
k3d image import nginx-openresty:latest wordpress-custom:latest mysql-custom:latest -c syfe-cluster

# Deploy
helm install my-release charts/wordpress/
helm install prometheus prometheus-community/prometheus
helm install grafana grafana/grafana --set adminPassword=admin123
```

## Access URLs

```bash
# WordPress
kubectl port-forward svc/my-release-wordpress-wordpress 8080:80
# http://localhost:8080

# Grafana (admin/admin123)
kubectl port-forward svc/grafana 3000:80
# http://localhost:3000

# Prometheus
kubectl port-forward svc/prometheus-server 9090:80
# http://localhost:9090

# Nginx Status
kubectl port-forward pod/my-release-wordpress-nginx-xxx 8081:80
# http://localhost:8081/nginx_status
```

## Key Metrics Available
- Pod CPU/Memory utilization
- Nginx request count and active connections
- Kubernetes cluster health
- Storage and network metrics

## Cleanup
```bash
helm delete my-release prometheus grafana
```


<img width="1862" height="1073" alt="Screenshot from 2025-11-12 22-51-31" src="https://github.com/user-attachments/assets/270e72e7-28f9-44e1-a59e-eff524edb1dc" />
<img width="1914" height="940" alt="Screenshot from 2025-11-12 22-01-32" src="https://github.com/user-attachments/assets/f23c916f-88fc-4a10-bcc0-2ba8391e369d" />
<img width="1914" height="1074" alt="Screenshot from 2025-11-12 22-11-08" src="https://github.com/user-attachments/assets/77233656-5a36-482d-897d-9c3a8a5e9746" />
<img width="1914" height="1074" alt="Screenshot from 2025-11-12 22-11-42" src="https://github.com/user-attachments/assets/553103b7-4214-4029-872a-7fa3f61b2ccb" />


<img width="1914" height="633" alt="Screenshot from 2025-11-12 22-02-50" src="https://github.com/user-attachments/assets/c53a66d1-7665-42a3-9aee-dca7595316e9" />
<img width="1914" height="633" alt="Screenshot from 2025-11-12 22-09-00" src="https://github.com/user-attachments/assets/02ad005d-e1ca-46e6-bd4f-363f8616f2a4" />
<img width="1914" height="1074" alt="Screenshot from 2025-11-12 22-09-12" src="https://github.com/user-attachments/assets/75911120-d147-43b7-bb3a-b496f180118d" />
<img width="1851" height="921" alt="Screenshot from 2025-11-12 22-27-01" src="https://github.com/user-attachments/assets/abed552a-e88e-4f32-8983-6f1f617913d4" />











**Status**: All objectives completed with production-grade implementation
