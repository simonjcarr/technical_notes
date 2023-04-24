These notes taken from https://docs.gitlab.com/ee/install/docker.html

## Set up the volumes location[](https://docs.gitlab.com/ee/install/docker.html#set-up-the-volumes-location "Permalink")

Before setting everything else, configure a new environment variable `$GITLAB_HOME` pointing to the directory where the configuration, logs, and data files will reside. Ensure that the directory exists and appropriate permission have been granted.

For Linux users, set the path to `/srv/gitlab`:

```
export GITLAB_HOME=/srv/gitlab
```

For macOS users, use the user’s `$HOME/gitlab` directory:

```
export GITLAB_HOME=$HOME/gitlab
```

The GitLab container uses host mounted volumes to store persistent data for data, logs, and config

## Create a standard Docker Compose file

Below is standard `docker-copmpose.yml` file that uses port 80, 443, and 22. if you don't want to use these ports see the [[#Create an Alternative Docker Compose File]]
```yaml
version: '3.6'
services:
  web:
    image: 'gitlab/gitlab-ee:latest'
    restart: always
    hostname: 'gitlab.example.com'
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'https://gitlab.example.com'
        # Add any other gitlab.rb configuration here, each on its own line
    ports:
      - '80:80'
      - '443:443'
      - '22:22'
    volumes:
      - '$GITLAB_HOME/config:/etc/gitlab'
      - '$GITLAB_HOME/logs:/var/log/gitlab'
      - '$GITLAB_HOME/data:/var/opt/gitlab'
    shm_size: '256m'
```

## Create an Alternative Docker Compose File

Below is another `docker-compose.yml` example with GitLab running on a custom HTTP and SSH port. Notice how the `GITLAB_OMNIBUS_CONFIG` variables match the `ports` section:

```yaml
version: '3.6'
services:
  web:
    image: 'gitlab/gitlab-ee:latest'
    restart: always
    hostname: 'gitlab.example.com'
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://gitlab.example.com:8929'
        gitlab_rails['gitlab_shell_ssh_port'] = 2224
    ports:
      - '8929:8929'
      - '2224:22'
    volumes:
      - '$GITLAB_HOME/config:/etc/gitlab'
      - '$GITLAB_HOME/logs:/var/log/gitlab'
      - '$GITLAB_HOME/data:/var/opt/gitlab'
    shm_size: '256m'
```

## Initial Login

Username: root

Get the initial root password with the following command

```bash
docker exec -it <container id> grep 'Password:' /etc/gitlab/initial_root_password
```

## Upgrade GitLab


```bash
docker compose pull
docker compose up -d
```
