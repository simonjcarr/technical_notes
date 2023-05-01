##### Get the public key
```
curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elastic.gpg
```

##### Add elasticsearch 8 to sources
```bash
echo "deb [signed-by=/usr/share/keyrings/elastic.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-8.x.list
```

#### Install
```bash
sudo apt install filebeat
```

### Configure Filebeat
```bash
sudo nano /etc/filebeat/filebeat.yml
```

Comment out `output.elasticsearch` and `hosts` so it looks like this
```yml
#output.elasticsearch:
  # Array of hosts to connect to.
  #hosts: ["localhost:9200"]

```

Now uncomment  `output.logstash` and `hosts` so it looks like this
```yml
output.logstash:
  # The Logstash hosts
  hosts: ["localhost:5044"]

```

Then save the file and exit back to the command prompt

#### Enable the filebeat system module
```bash
sudo filebeat modules enable system
```

#### Load the index template
```bash
sudo filebeat setup --index-management -E output.logstash.enabled=false -E 'output.elasticsearch.hosts=["<Your elk server IP>:9200"]'
```

#### Start the Filebeat service
```bash
sudo systemctl enable filebeat --now
sudo systemctl status filebeat
```

#### Verify elasticsearch is receiving data
```bash
curl -XGET http://<elk server IP>/_cat/indices?y
```