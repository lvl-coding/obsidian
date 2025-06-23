```
du -sh /* --exclude="proc"
lsb_release -a
lvextend -l +100%FREE /dev/mapper/ubuntu--vg-ubuntu--lv 
resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv
ss -antlp
```

```
cat sudoers.d/kvirtadm
kvirtadm ALL=(ALL)       NOPASSWD: ALL
```


```
docker run \
  --name "/cinder_volume_dev" \
  --privileged \
  --runtime "runc" \
  --volume "/etc/kolla/cinder-volume/:/var/lib/kolla/config_files/:ro" \
  --volume "/etc/localtime:/etc/localtime:ro" \
  --volume "/etc/timezone:/etc/timezone:ro" \
  --volume "/dev/:/dev/:rw" \
  --volume "/lib/modules:/lib/modules:ro" \
  --volume "/run/:/run/:shared" \
  --volume "cinder:/var/lib/cinder:rw" \
  --volume "kolla_logs:/var/log/kolla/:rw" \
  --log-driver "json-file" \
  --log-opt max-file="5" \
  --log-opt max-size="50m" \
  --restart "unless-stopped" \
  --network "host" \
  --hostname "cluster89" \
  --user "root" \
  --env "KOLLA_CONFIG_STRATEGY=COPY_ALWAYS" \
  --env "KOLLA_SERVICE_NAME=cinder-volume" \
  --env "PATH=/var/lib/kolla/venv/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin" \
  --env "LANG=en_US.UTF-8" \
  --env "KOLLA_BASE_DISTRO=ubuntu" \
  --env "KOLLA_DISTRO_PYTHON_VERSION=3.8" \
  --env "KOLLA_BASE_ARCH=x86_64" \
  --env "PS1=$(tput bold)($(printenv KOLLA_SERVICE_NAME))$(tput sgr0)[$(id -un)@$(hostname -s) $(pwd)]$ " \
  --env "DEBIAN_FRONTEND=noninteractive" \
  --env "KOLLA_INSTALL_TYPE=source" \
  --env "KOLLA_INSTALL_METATYPE=mixed" \
  --label "build-date"="20250616" \
  --label "kolla_version"="12.4.1" \
  --label "maintainer"="Kolla Project (https://launchpad.net/kolla)" \
  --label "name"="cinder-volume" \
  --label "org.opencontainers.image.ref.name"="ubuntu" \
  --label "org.opencontainers.image.version"="20.04" \
  --detach \
  "192.168.10.89:7777/openstack/kolla/ubuntu-source-cinder-volume:wallaby-release-3.0" \
  sleep infinity
```