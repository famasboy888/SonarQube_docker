# SonarQube server deployed on Docker

SonarQube architecture from [Official Installation](https://docs.sonarsource.com/sonarqube/latest/setup-and-upgrade/install-the-server/introduction/)

<p align="center">
  <img width="50%" height="50%" src="https://github.com/famasboy888/SonarQube_docker/assets/23441168/90047c3e-35f6-480f-9947-b4ca4bd9cc77">
</p>

## 1) Pre-requisite for successful deployment

Edit `/etc/sysctl.conf`
```bash
sudo nano /etc/sysctl.conf
```

Add the following:
```bash
vm.max_map_count=262144
fs.file-max=65536
```

Apply changes:
```bash
sudo sysctl -p
```

## 2) Install `docker` and `docker-compose`

_Note: I am using Debian distro_

```bash
sudo apt update
sudo apt install docker.io docker-compose -y
```

Changing permmission to execute docker as non-root

```bash
sudo chmod 666 /var/run/docker.sock
```

## 3) Create a docker compose YAML file

_Note: Remove comment explainations_

sonarqube-compose.yaml

```yaml
version: "3"
services:
  sonarqube:
    image: sonarqube:community                                  #We pull community image from Dockerhub
    restart: unless-stopped
    depends_on:                                                 #We specify that it is dependent on another service called 'db'
      - db
    environment:
      SONAR_JDBC_URL: jdbc:postgresql://db:5432/sonar           #Specify required env variables. PostgreSQL works at port 5432
      SONAR_JDBC_USERNAME: sonar
      SONAR_JDBC_PASSWORD: sonar
    volumes:
      - sonarqube_data:/opt/sonarqube/data                      #Specify local volume stored
      - sonarqube_extensions:/opt/sonarqube/extensions
      - sonarqube_logs:/opt/sonarqube/logs
    ports:                                                      #Specify 9000 as running port for SonarQube
      - "9000:9000"
  db:
    image: postgres:12                                          #Image is pulling version 12
    restart: unless-stopped
    environment:
      POSTGRES_USER: sonar
      POSTGRES_PASSWORD: sonar
    volumes:
      - postgresql:/var/lib/postgresql
      - postgresql_data:/var/lib/postgresql/data
volumes:
  sonarqube_data:
  sonarqube_extensions:
  sonarqube_logs:
  postgresql:
  postgresql_data:
```

## 4) Run docker compose file

```bash
docker-compose -f sonarqube-compose.yaml up -d
```

Wait for it to finish pulling and downloading images

<p align="center">
  <img width="40%" height="40%" src="https://github.com/famasboy888/SonarQube_docker/assets/23441168/e6bc3549-e04a-4ed8-8181-ad5e1872e71e">
</p>

Watch live logs
```bash
docker-compose -f sonarqube-compose.yaml logs --follow
```

```bash
Output:

sonarqube_1  | 2024.04.25 01:46:50 WARN  app[][startup] ####################################################################################################################
sonarqube_1  | 2024.04.25 01:46:50 WARN  app[][startup] Default Administrator credentials are still being used. Make sure to change the password or deactivate the account.
sonarqube_1  | 2024.04.25 01:46:50 WARN  app[][startup] ####################################################################################################################
sonarqube_1  | 2024.04.25 01:46:50 INFO  web[][o.s.s.p.Platform] Web Server is operational
sonarqube_1  | 2024.04.25 01:46:50 INFO  ce[][o.s.d.DefaultDatabase] Create JDBC data source for jdbc:postgresql://db:5432/sonar
sonarqube_1  | 2024.04.25 01:46:50 INFO  ce[][c.z.h.HikariDataSource] HikariPool-1 - Starting...
sonarqube_1  | 2024.04.25 01:46:50 INFO  ce[][c.z.h.p.HikariPool] HikariPool-1 - Added connection org.postgresql.jdbc.PgConnection@25f735a5
sonarqube_1  | 2024.04.25 01:46:50 INFO  ce[][c.z.h.HikariDataSource] HikariPool-1 - Start completed.
sonarqube_1  | 2024.04.25 01:46:52 INFO  ce[][o.s.s.p.ServerFileSystemImpl] SonarQube home: /opt/sonarqube
sonarqube_1  | 2024.04.25 01:46:52 INFO  ce[][o.s.c.c.CePluginRepository] Load plugins
sonarqube_1  | 2024.04.25 01:46:55 INFO  ce[][o.s.c.c.ComputeEngineContainerImpl] Running Community edition
sonarqube_1  | 2024.04.25 01:46:55 INFO  ce[][o.s.ce.app.CeServer] Compute Engine is started
sonarqube_1  | 2024.04.25 01:46:55 INFO  app[][o.s.a.SchedulerImpl] Process[ce] is up
sonarqube_1  | 2024.04.25 01:46:55 INFO  app[][o.s.a.SchedulerImpl] SonarQube is operational
```
