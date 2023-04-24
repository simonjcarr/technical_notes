```bash
curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elastic.gpg
```

```bash
echo "deb [signed-by=/usr/share/keyrings/elastic.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-8.x.list
```

```bash
sudo apt update
```

#### Install filebeat
```bash
sudo apt install filebeat
```

#### Change filebeat config
```bash
nano /etc/filebeat/filebeat.yml
```

Comment out the `output.elasticsearch` and `hosts`
```yml
#output.elasticsearch:
  # Array of hosts to connect to.
  #hosts: ["localhost:9200"]

```

Uncomment  `output.logstash` and `hosts`. Change the host ip address to that of your ELK stack. i.e.
```yml
output.logstash:
  # The Logstash hosts
  hosts: ["192.168.1.172:5044"]

```