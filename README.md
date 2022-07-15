## Prometheus
Deploying prometheus on Kubernetes  
Download the prometheus client  
`wget https://github.com/prometheus/prometheus/releases/download/v2.37.0/prometheus-2.37.0.linux-amd64.tar.gz`

AlertManager  
`wget https://github.com/prometheus/alertmanager/releases/download/v0.24.0/alertmanager-0.24.0.linux-amd64.tar.gz`

node_exporter  
`wget https://github.com/prometheus/node_exporter/releases/download/v1.3.1/node_exporter-1.3.1.linux-amd64.tar.gz`

Extract the archive  
`tar xvfz prometheus-2.37.0.linux-amd64.tar.gz`

Navigate to the directory & edit the prometheus.yml file.  
To run prometheus:  
`./prometheus --config.file=prometheus.yml`

Get the public IP/ DNS of the instance (`http://localhost:9090` if running locally) and on the browser:  
`3.91.195.193:9090`  
`http://ec2-3-91-195-193.compute-1.amazonaws.com:9090`

To view data on the expression browser, enter the command below to view how many times the metrics endpoint has been called:  
`promhttp_metric_handler_requests_total`

To view data through the graphing interface, select the "Graph" tab and execute the below command:  
`rate(promhttp_metric_handler_requests_total{code="200"}[1m])`

## Collecting Data From Clients
Download these go clients and run them:
```
git clone https://github.com/prometheus/client_golang.git
cd client_golang/examples/random
go get -d
go build

# Start 3 example targets in separate terminals:
./random -listen-address=:8080
./random -listen-address=:8081
./random -listen-address=:8082
```

Add these clients in the `prometheus.yml`:
```
scrape_configs:
  - job_name:       'example-random'

    # Override the global default and scrape targets from this job every 5 seconds.
    scrape_interval: 5s

    static_configs:
      - targets: ['localhost:8080', 'localhost:8081']
        labels:
          group: 'production'

      - targets: ['localhost:8082']
        labels:
          group: 'canary'
```

Restart prometheus and view this metric in the expression browser:  
`rpc_durations_seconds`