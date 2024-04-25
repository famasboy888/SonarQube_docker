# SonarQube server deployed on Docker

SonarQube architecture from [Official Installation](https://docs.sonarsource.com/sonarqube/latest/setup-and-upgrade/install-the-server/introduction/)

<p align="center">
  <img width="50%" height="50%" src="https://github.com/famasboy888/SonarQube_docker/assets/23441168/90047c3e-35f6-480f-9947-b4ca4bd9cc77">
</p>

## 1) Pre-requisite for successful deployment

Edit `/etc/sysctl.conf`

Add the following:
```bash
vm.max_map_count=262144
fs.file-max=65536
```
