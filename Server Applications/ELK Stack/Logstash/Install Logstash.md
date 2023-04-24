##### Get the public key
```
curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elastic.gpg
```

##### Add elasticsearch 8 to sources
```bash
echo "deb [signed-by=/usr/share/keyrings/elastic.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
```

#### Install Logstash
```bash
sudo apt install logstash
```

#### Start and Enable the Logstash service
```bash
sudo systemctl enable logstash --now
sudo systemctl status logstash
```

