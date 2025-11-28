# GrafanaAlloyDemos
This repository contains various exampled and demos of Grafana Alloy

# Workshop Steps for Grafana Alloy MacOS System Level Logs

1. Set Up Loki and Grafana Stack by running `docker compose up -d`

    - Access Grafana at http://localhost:3000 (admin/admin), add Loki datasource at http://loki:3100 this is to add in the Grafana Data Source
    - To verify `loki` is working `curl http://localhost:3100/ready` and/or `curl http://localhost:3100/metrics | head -20` or even without curl on the browser
    - User Interface of `alloy` is available at `http://localhost:12345/`, you can change this default port if required

2. Configure Alloy for macOS Service Logs in config.alloy file

    - This setup tells Alloy to tail and send logs from /var/log/*.log files, filter out noisy lines, and push them to Loki.

3. Apply Alloy configuration and start Alloy

    - Copy your config file to the Alloy default config location `sudo cp config.alloy $(brew --prefix)/etc/alloy/config.alloy`
    - Reload Alloy configuration without restart `curl -X POST http://localhost:12345/-/reload` or if you wish to restart the alloy service `brew services restart grafana/grafana/alloy`
    - On Linux the command `systemctl` provides the config file location, but it's not a straightforward option when it comes to Mac OS. Run the below script to show what is the alloy file that the current running alloy service is using 
    
    ```shell
        echo "Alloy Config File:" && \
        ps aux | grep '[a]lloy.*config' | awk '{for(i=11;i<=NF;i++) printf "%s ",$i; print ""}' && \
        echo "Config Path:" "$(brew --prefix)/etc/alloy/config.alloy" && \
        echo "Service Status:" && brew services info grafana/grafana/alloy
    ```

4. Verify logs in Grafana

    - Open Grafana at http://localhost:3000. Go to Explore, select Loki as the data source, browse labels to select files Alloy is sending logs for, and verify logs are visible

# Workshop Steps for Grafana Alloy Docker Container Logs

1. Copy your config file to the Alloy default config location `sudo cp docker.alloy $(brew --prefix)/etc/alloy/config.alloy`

    - This will override the existing config.alloy file as at any given time alloy can run only one alloy configuration file

2. Reload Alloy configuration without restart `curl -X POST http://localhost:12345/-/reload` or if you wish to restart the alloy service `brew services restart grafana/grafana/alloy`

# Workshop Steps for Grafana Alloy Kubernetes Container Logs

