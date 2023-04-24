##### Get the public key
```
curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elastic.gpg
```

##### Add elasticsearch 8 to sources
```bash
echo "deb [signed-by=/usr/share/keyrings/elastic.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
```

#### Install Kibana
```bash
sudo apt install kibana
```

#### Start the Kibana service
```bash
sudo systemctl enable kibana --now
sudo systemctl status kibana
```

#### Configure Kibana
```bash
sudo nano /etc/kibana/kibana.yml
```

* Uncomment `server.port`
* Uncomment `server.host` and change the value to `0.0.0.0`
* Uncomment `elasticsearch.hosts` leave the value as `http://localhost:9200`
Save the file
Restart the Kibana service
```bash
sudo systemctl restart kibana
sudo systemctl status kibana
```

#### Open Kibana in the browser

Open Kibana in your browser at `http://elk_ip_addesss:5601`