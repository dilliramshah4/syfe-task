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

**Status**: All objectives completed with production-grade implementation
