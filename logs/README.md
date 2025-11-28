# GrafanaAlloyDemos
This repository contains various exampled and demos of Grafana Alloy

# Workshop Steps for Grafana Alloy MacOS System Level Logs

1. Set Up Loki and Grafana Stack by running `docker compose up -d`

    - Access Grafana at http://localhost:3000 (admin/admin), add Loki datasource at http://loki:3100

2. Configure Alloy for macOS Service Logs in config.alloy file

    - This setup tells Alloy to tail and send logs from /var/log/*.log files, filter out noisy lines, and push them to Loki.

3. Apply Alloy configuration and start Alloy

    - Copy your config file to the Alloy default config location `sudo cp config.alloy $(brew --prefix)/etc/alloy/config.alloy`
    - Reload Alloy configuration without restart `curl -X POST http://localhost:12345/-/reload` or if you wish to restart the alloy service `brew services restart grafana/grafana/alloy`

4. Verify logs in Grafana

    - Open Grafana at http://localhost:3000. Go to Explore, select Loki as the data source, browse labels to select files Alloy is sending logs for, and verify logs are visible

# Workshop Steps for Grafana Alloy Docker Container Logs


# Workshop Steps for Grafana Alloy Kubernetes Container Logs

