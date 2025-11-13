# WordPress Application Monitoring Metrics

##  **DEPLOYED MONITORING STACK**
- **Prometheus**:  Running (metrics collection)
- **Grafana**:  Running (visualization) - admin/admin123
- **AlertManager**:  Running (alerting)
- **Node Exporter**:  Running (system metrics)

## **REQUIRED METRICS IMPLEMENTATION**

### 1. **Pod CPU Utilization** 
**Metric**: `rate(container_cpu_usage_seconds_total[5m]) * 100`
- **Source**: cAdvisor (built into Kubernetes)
- **Alert**: > 80% for 2 minutes
- **Visualization**: Time series graph in Grafana
- **Query Example**: 
  ```promql
  rate(container_cpu_usage_seconds_total{pod=~".*wordpress.*"}[5m]) * 100
  ```

### 2. **Nginx Monitoring** 

#### Total Request Count
- **Metric**: `nginx_http_requests_total`
- **Source**: Nginx stub_status module
- **Endpoint**: `/nginx_status`
- **Visualization**: Counter showing total requests
- **Query Example**:
  ```promql
  increase(nginx_http_requests_total[5m])
  ```

#### Total 5xx Requests
- **Metric**: `nginx_http_requests_total{status=~"5.."}`
- **Source**: Nginx access logs / stub_status
- **Alert**: > 0.1 requests/second for 1 minute
- **Visualization**: Error rate percentage
- **Query Example**:
  ```promql
  rate(nginx_http_requests_total{status=~"5.."}[5m])
  ```

##  **COMPLETE METRICS CATALOG**

### **WordPress Application Metrics**

#### Container Metrics
- **CPU Usage**: `container_cpu_usage_seconds_total`
- **Memory Usage**: `container_memory_usage_bytes`
- **Memory Limit**: `container_spec_memory_limit_bytes`
- **Network I/O**: `container_network_receive_bytes_total`, `container_network_transmit_bytes_total`
- **Filesystem Usage**: `container_fs_usage_bytes`

#### Pod Metrics
- **Pod Status**: `kube_pod_status_phase`
- **Pod Restarts**: `kube_pod_container_status_restarts_total`
- **Pod Ready**: `kube_pod_status_ready`
- **Pod Age**: `kube_pod_created`

### **Apache/WordPress Metrics**

#### HTTP Metrics
- **Request Rate**: `apache_http_requests_total`
- **Response Codes**: `apache_http_requests_total` (by status code)
- **Request Duration**: `apache_duration_seconds_total`
- **Active Connections**: `apache_connections`

#### PHP Metrics
- **PHP-FPM Processes**: `phpfpm_active_processes`
- **PHP Memory Usage**: `php_memory_usage_bytes`
- **PHP Execution Time**: `php_execution_time_seconds`

### **Nginx Metrics**

#### Connection Metrics
- **Active Connections**: `nginx_connections_active`
- **Accepted Connections**: `nginx_connections_accepted`
- **Handled Connections**: `nginx_connections_handled`
- **Reading/Writing/Waiting**: `nginx_connections_reading`, `nginx_connections_writing`, `nginx_connections_waiting`

#### Request Metrics
- **Total Requests**: `nginx_http_requests_total`
- **Request Rate**: `rate(nginx_http_requests_total[5m])`
- **Response Codes**: `nginx_http_requests_total{status="200|404|500"}`
- **Request Size**: `nginx_http_request_size_bytes`
- **Response Size**: `nginx_http_response_size_bytes`

#### Performance Metrics
- **Request Duration**: `nginx_http_request_duration_seconds`
- **Upstream Response Time**: `nginx_upstream_response_time_seconds`
- **SSL Handshake Time**: `nginx_ssl_handshake_time_seconds`

### **MySQL Database Metrics**

#### Connection Metrics
- **Current Connections**: `mysql_global_status_threads_connected`
- **Max Connections**: `mysql_global_variables_max_connections`
- **Connection Errors**: `mysql_global_status_connection_errors_total`
- **Aborted Connections**: `mysql_global_status_aborted_connects`

#### Query Metrics
- **Query Rate**: `rate(mysql_global_status_queries[5m])`
- **Slow Queries**: `mysql_global_status_slow_queries`
- **Select/Insert/Update/Delete**: `mysql_global_status_commands_total`

#### Performance Metrics
- **InnoDB Buffer Pool Hit Rate**: `mysql_global_status_innodb_buffer_pool_read_requests / mysql_global_status_innodb_buffer_pool_reads`
- **Table Locks**: `mysql_global_status_table_locks_waited`
- **Query Cache Hit Rate**: `mysql_global_status_qcache_hits / (mysql_global_status_qcache_hits + mysql_global_status_qcache_inserts)`

##  **ALERTING RULES**

### Critical Alerts
```yaml
- alert: PodCrashLooping
  expr: rate(kube_pod_container_status_restarts_total[15m]) > 0
  for: 5m
  severity: critical

- alert: HighErrorRate
  expr: rate(nginx_http_requests_total{status=~"5.."}[5m]) > 0.1
  for: 1m
  severity: critical

- alert: DatabaseDown
  expr: mysql_up == 0
  for: 1m
  severity: critical
```

### Warning Alerts
```yaml
- alert: HighCPUUsage
  expr: rate(container_cpu_usage_seconds_total{pod=~".*wordpress.*"}[5m]) * 100 > 80
  for: 2m
  severity: warning

- alert: HighMemoryUsage
  expr: container_memory_usage_bytes / container_spec_memory_limit_bytes * 100 > 80
  for: 2m
  severity: warning
```

## **GRAFANA DASHBOARDS**

### WordPress Overview Dashboard
1. **Request Rate**: Total HTTP requests/second
2. **Error Rate**: 4xx/5xx error percentage  
3. **Response Time**: Average response time
4. **CPU Usage**: Pod CPU utilization
5. **Memory Usage**: Pod memory utilization
6. **Database Connections**: Active MySQL connections

### Infrastructure Dashboard
1. **Cluster Resources**: Node CPU/Memory/Disk
2. **Pod Health**: Running/Failed pod counts
3. **Network Traffic**: Ingress/Egress bandwidth
4. **Storage Usage**: PV utilization
5. **Alert Summary**: Active alerts

##  **ACCESS MONITORING**

```bash
# Grafana Dashboard
kubectl port-forward svc/grafana 3000:80
# Visit: http://localhost:3000 (admin/admin123)

# Prometheus Metrics
kubectl port-forward svc/prometheus-server 9090:80  
# Visit: http://localhost:9090

# Nginx Status
kubectl port-forward svc/my-release-wordpress-nginx 8080:80
# Visit: http://localhost:8080/nginx_status
```

##  **IMPLEMENTATION STATUS**
- [x] Prometheus deployed and collecting metrics
- [x] Grafana deployed with dashboards
- [x] Pod CPU utilization monitoring
- [x] Nginx request count tracking
- [x] Nginx 5xx error monitoring
- [x] AlertManager for notifications
- [x] Complete metrics documentation
- [x] Access endpoints configured
