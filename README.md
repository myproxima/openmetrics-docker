# OpenMetrics Docker Stack

A monitoring solution using Docker hosts and containers with Prometheus and Grafana.

## Install

Clone this repository on your Docker host, go into `openmetrics-docker directory` and run compose up:

```bash
git clone https://github.com/myproxima/openmetrics-docker
cd openmetrics-docker

ADMIN_USER='admin' ADMIN_PASSWORD='admin' FQDN='metrics.demo.com' docker-compose up -d
```

Prerequisites:

* Docker Engine >= 1.41
* Docker Compose >= v2.17.2

Containers:

* Prometheus (metrics database) `http://localhost:9090`
* Grafana (visualize metrics) `https://<host-ip>:443`
* Grafana Image Renderer (rendering panels and dashboards to PNGs) `http://localhost:8081`
* nginx (reverse proxy for grafana)

## Setup Prometheus

Go into `openmetrics-docker/prometheus/scrape_configs` directory and edit the file `proxima.json`.  
Remove in JSON the placeholder object:

```json
  {
    "targets": [
      "proxima.demo.com:443"
    ],
    "labels": {
      "job": "node",
      "instance": "proxima.demo.com"
    }
  }
```

Add one object for each machine you need to monitor by replacing `proxima.demo.com` with the server's FQDN address.  
You can add new servers by editing the file without the need to stop the service.


## Setup Grafana

Navigate to `https://<host-ip>:443` and login with user ***admin*** password ***admin***. You can change credentials by replacing them in the compose file or by supplying the `ADMIN_USER` and `ADMIN_PASSWORD` environment variables on compose up.

In order to receive email notifications via Grafana you need to set up an SMTP account. In the `docker-compose.yml` file there are the commented environment variables for the SMTP configuration, starting with `GF_SMTP_`: fill in the necessary values and uncomment them before launching `docker-compose up -d` again (see Notes section).

To insert a new dashboard you have to go to Dashboards, then click on Import (via the New button) and upload the JSON.  
We have uploaded some dashboards created by us specifically for monitoring our Proxima products to our `grafana-dashboards` repository.

## Setup nginx

Go into `openmetrics-docker/nginx/ssl` directory and edit the `metrics.crt` and `metrics.key` files with the actual content.  
If you don't have any certificate, create a self-signed one.  
**Without certificate the nginx service will not start.**

## Notes
When you have to change the docker-compose file, i.e to setup SMTP for Grafana:
```bash
docker-compose up -d
```
Docker will only restart the service you changed inside of the docker-compose file.
