### Running in a LXC Container?
If running in an LXC Container read this first [[Running Elasticsearch in an LXC container]]

#### Install apt-transport-https
```
sudo apt install apt-transport-https
```

#### Install Java
```
sudo apt install openjdk-17-jdk
java -version
```

#### Update environment
```
sudo nano /etc/environment
```
Add the following lines
```
JAVA_HOME="/usr/lib/jvm/java-17-openjdk-amd64"
ES_JAVA_HOME="/usr/lib/jvm/java-17-openjdk-amd64"
```
Save and close, then source the environment file
```
source /etc/environment
echo $JAVA_HOME
```

####
Install gpg
```
sudo apt install gpg
```

#### Install Elastic Search
##### Get the public key
```
curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elastic.gpg
```

##### Add elasticsearch 8 to sources
```bash
echo "deb [signed-by=/usr/share/keyrings/elastic.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-8.x.list
```

##### Install
```bash
sudo apt update
sudo apt install elasticsearch
```

#### Start the service
```bash
sudo systemctl enable elasticsearch --now
sudo systemctl status elasticsearch
```

#### Configure Elasticsearch
```bash
sudo nano /etc/elasticsearch/elasticsearch.yml
```

Make the following changes
* Set `network.host` to `0.0.0.0`
* Uncomment discovery.seed_hosts and set the array to contain 127.0.0.1
	```yml
	discovery.seed_hosts: ["127.0.0.1"]
```
* change `xpack.security.enabled` to `false`
	```yml
	xpack.security.enabled: false
```

#### Restart Elasticsearch
```bash
sudo systemctl restart elasticsearch
```

#### Test Elasticsearch
```bash
curl -X GET "localhost:9200"
```
The result should look something like this
```console
{
  "name" : "elk",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "pyYb7qJFQv6wTJRmLBUZoA",
  "version" : {
    "number" : "8.7.0",
    "build_flavor" : "default",
    "build_type" : "deb",
    "build_hash" : "09520b59b6bc1057340b55750186466ea715e30e",
    "build_date" : "2023-03-27T16:31:09.816451435Z",
    "build_snapshot" : false,
    "lucene_version" : "9.5.0",
    "minimum_wire_compatibility_version" : "7.17.0",
    "minimum_index_compatibility_version" : "7.0.0"
  },
  "tagline" : "You Know, for Search"
}

```

