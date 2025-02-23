# Full-Stack Monitoring Project with Prometheus and Grafana

This project demonstrates a complete monitoring setup using Prometheus and Grafana, along with a sample application that we'll monitor. We'll build everything from scratch and implement comprehensive monitoring.

## Project Components

1. **Sample Application Stack**
   - Node.js REST API with Express
   - MongoDB for data storage
   - Nginx as reverse proxy
   - Custom business logic with measurable metrics

2. **Monitoring Stack**
   - Prometheus for metrics collection
   - Grafana for visualization
   - Node Exporter for system metrics
   - Custom exporters and instrumentation

## Project Structure

```
monitoring/
├── app/                      # Sample Application
│   ├── src/
│   │   ├── routes/          # API routes
│   │   ├── models/          # Data models
│   │   ├── metrics/         # Custom metrics
│   │   └── middleware/      # Custom middleware
│   ├── tests/               # Application tests
│   ├── app.js              # Main application file
│   └── package.json        # Dependencies
├── nginx/
│   └── nginx.conf          # Nginx configuration
├── prometheus/
│   ├── prometheus.yml      # Prometheus configuration
│   └── alert.rules.yml     # Alerting rules
├── grafana/
│   └── dashboards/         # Dashboard JSON files
└── scripts/                # Setup and maintenance scripts
```

## Detailed Implementation Plan

### Phase 1: Sample Application Setup

1. **Basic Express Application**
   ```javascript
   // app.js structure
   const express = require('express');
   const prometheus = require('prom-client');
   const mongoose = require('mongoose');
   
   // Custom metrics
   const httpRequestDuration = new prometheus.Histogram({...});
   const activeUsers = new prometheus.Gauge({...});
   const businessTransactions = new prometheus.Counter({...});
   ```

2. **API Endpoints**
   - `/api/users` - CRUD operations
   - `/api/products` - Product management
   - `/api/orders` - Order processing
   - `/metrics` - Prometheus metrics endpoint

3. **Business Metrics to Monitor**
   - Active user sessions
   - Order processing time
   - Transaction success rate
   - API endpoint usage
   - Error rates by endpoint
   - Database operation latency

### Phase 2: Infrastructure Setup

1. **Server Requirements**
   ```
   Primary Server (Application + Monitoring):
   - Ubuntu 22.04 LTS
   - 4GB RAM minimum
   - 30GB SSD storage
   ```

2. **Network Configuration**
   ```
   Ports:
   - 80/443: Nginx
   - 3000: Node.js application
   - 9090: Prometheus
   - 3000: Grafana
   - 9100: Node Exporter
   - 27017: MongoDB
   ```

### Phase 3: Monitoring Implementation

1. **Application Instrumentation**
   ```javascript
   // Example metrics setup
   const metrics = {
     httpRequestDuration: new prometheus.Histogram({
       name: 'http_request_duration_seconds',
       help: 'Duration of HTTP requests in seconds',
       labelNames: ['method', 'route', 'status_code'],
       buckets: [0.1, 0.5, 1, 2, 5]
     }),
     
     activeUsers: new prometheus.Gauge({
       name: 'app_active_users',
       help: 'Number of currently active users'
     }),
     
     dbOperationDuration: new prometheus.Histogram({
       name: 'db_operation_duration_seconds',
       help: 'Duration of database operations',
       labelNames: ['operation', 'collection'],
       buckets: [0.01, 0.05, 0.1, 0.5, 1]
     })
   };
   ```

2. **Prometheus Configuration**
   ```yaml
   # prometheus.yml
   global:
     scrape_interval: 15s
     evaluation_interval: 15s

   scrape_configs:
     - job_name: 'nodejs_app'
       static_configs:
         - targets: ['localhost:3000']
       metrics_path: '/metrics'
       
     - job_name: 'node_exporter'
       static_configs:
         - targets: ['localhost:9100']
         
     - job_name: 'mongodb_exporter'
       static_configs:
         - targets: ['localhost:9216']
   ```

3. **Alert Rules**
   ```yaml
   # alert.rules.yml
   groups:
   - name: application_alerts
     rules:
     - alert: HighErrorRate
       expr: rate(http_requests_total{status_code=~"5.."}[5m]) > 1
       for: 2m
       labels:
         severity: critical
       annotations:
         summary: High error rate detected
         
     - alert: SlowAPIEndpoint
       expr: http_request_duration_seconds{quantile="0.9"} > 2
       for: 5m
       labels:
         severity: warning
       annotations:
         summary: Slow API endpoint detected
   ```

### Phase 4: Grafana Dashboards

1. **Application Dashboard**
   - Request Rate by Endpoint
   - Error Rate
   - Response Time Percentiles
   - Active Users
   - Business Metrics

2. **System Dashboard**
   - CPU Usage
   - Memory Usage
   - Disk I/O
   - Network Traffic
   - System Load

3. **MongoDB Dashboard**
   - Connection Pool Status
   - Operation Latency
   - Collection Metrics
   - Index Usage
   - Cache Statistics

### Phase 5: Testing and Validation

1. **Load Testing Script**
   ```javascript
   // loadtest.js
   const autocannon = require('autocannon');
   
   const test = autocannon({
     url: 'http://localhost:3000',
     connections: 100,
     duration: 30,
     scenarios: [
       { method: 'GET', path: '/api/products' },
       { method: 'POST', path: '/api/orders' }
     ]
   });
   ```

2. **Metrics Validation**
   - Verify metric collection
   - Validate alert triggers
   - Check dashboard accuracy

### Phase 6: Documentation

1. **Setup Guide**
   - Installation steps
   - Configuration details
   - Security considerations

2. **Maintenance Guide**
   - Backup procedures
   - Update processes
   - Troubleshooting steps

## Getting Started

1. **Clone the Repository**
   ```bash
   git clone <repository-url>
   cd monitoring
   ```

2. **Install Dependencies**
   ```bash
   # Application dependencies
   cd app
   npm install

   # Monitoring tools
   # (Prometheus and Grafana installation scripts will be provided)
   ```

3. **Start the Stack**
   ```bash
   # Start MongoDB
   docker run -d -p 27017:27017 mongo:latest

   # Start the application
   cd app
   npm start

   # Start Prometheus
   ./scripts/start-prometheus.sh

   # Start Grafana
   ./scripts/start-grafana.sh
   ```

## Development Workflow

1. **Local Development**
   - Run the stack locally
   - Use development configuration
   - Monitor development metrics

2. **Testing**
   - Unit tests for application
   - Integration tests
   - Load tests with metrics validation

3. **Production Deployment**
   - Secure configuration
   - Production-ready settings
   - Backup and recovery setup

## Monitoring Best Practices

1. **Metric Naming**
   - Use consistent naming conventions
   - Include relevant labels
   - Follow Prometheus naming best practices

2. **Alert Design**
   - Avoid alert fatigue
   - Set appropriate thresholds
   - Include clear descriptions

3. **Dashboard Organization**
   - Logical grouping of metrics
   - Clear visualization choices
   - Consistent layout

## Troubleshooting Guide

1. **Common Issues**
   - Metric collection failures
   - Alert misfires
   - Dashboard rendering issues
   - Application performance problems

2. **Resolution Steps**
   - Diagnostic procedures
   - Log analysis
   - Configuration validation

## License

MIT License - Feel free to use this project for learning and development purposes.

## Contributing

Contributions are welcome! Please read our contributing guidelines for details on our code of conduct and the process for submitting pull requests.