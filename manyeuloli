#!/bin/bash
sudo apt-get update -y
sudo apt-get upgrade -y
sudo apt-get install -y wget curl vim ufw
sudo ufw allow 22/tcp
sudo ufw allow 3000/tcp
sudo ufw allow 9090/tcp
sudo ufw enable
wget https://github.com/prometheus/prometheus/releases/download/v2.52.0/prometheus-2.52.0.linux-amd64.tar.gz
tar xvzf prometheus-2.52.0.linux-amd64.tar.gz
sudo mv prometheus-2.52.0.linux-amd64 /usr/local/prometheus
rm prometheus-2.52.0.linux-amd64.tar.gz
sudo bash -c 'cat <<EOF > /etc/systemd/system/prometheus.service
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=root
Group=root
Type=simple
ExecStart=/usr/local/prometheus/prometheus \\
--config.file /usr/local/prometheus/prometheus.yml \\
--storage.tsdb.path /usr/local/prometheus/ \\
--web.console.templates=/usr/local/prometheus/consoles \\
--web.console.libraries=/usr/local/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
EOF'
systemctl daemon-reload
systemctl start prometheus
systemctl enable prometheus
wget https://dl.grafana.com/enterprise/release/grafana-enterprise_11.0.0_amd64.deb
dpkg -i grafana-enterprise_11.0.0_amd64.deb
apt-get install -f -y
rm grafana-enterprise_11.0.0_amd64.deb
systemctl daemon-reload
systemctl start grafana-server
systemctl enable grafana-server
GRAFANA_URL="http://localhost:3000"
GRAFANA_USER="admin"
GRAFANA_PASS="admin"
PROMETHEUS_URL="http://localhost:9090"
sleep 30
curl -X POST -H "Content-Type: application/json" -d '{
  "name": "Prometheus",
  "type": "prometheus",
  "url": "'"$PROMETHEUS_URL"'",
  "access": "proxy",
  "basicAuth": false
}' $GRAFANA_URL/api/datasources -u $GRAFANA_USER:$GRAFANA_PASS
