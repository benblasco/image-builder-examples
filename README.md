# image-builder-examples
Example blueprints and instructions for use of Image Builder for creating Enterprise Linux images

# Useful links

Super useful blog from Major Hayden
https://major.io/p/build-custom-centos-stream-cloud-image/

# Install and configure
```

dnf install -y osbuild-composer composer-cli cockpit-composer bash-completion

systemctl enable --now osbuild-composer.socket
systemctl enable --now cockpit.socket

source /etc/bash_completion.d/composer-cli

```

# Add an external repo

```
cat epel-testing-9.toml 
```

```
id = "epel-testing"
name = "Extra Packages for Enterprise Linux $releasever - Testing - $basearch"
type = "yum-baseurl"
distros = ["rhel-9"]
url = "https://mirrors.fedoraproject.org/metalink?repo=testing-epel$releasever&arch=$basearch&infra=$infra&content=$contentdir"
check_gpg = false
check_ssl = false
system = false
```

```
composer-cli sources add  epel-testing-9.toml
composer-cli sources list
```

# Create a blueprint

```
composer-cli blueprints push rhel92-partition-blueprint.toml
composer-cli blueprints depsolve rhel92-partition-blueprint
composer-cli compose start rhel92-partition-blueprint qcow2
composer-cli compose status
watch -n 5 composer-cli compose status

journalctl -u osbuild-worker@1
journalctl -xe -f -u osbuild-worker@1.service 

```

Download the file
```
composer-cli compose image <UUID>
```


### Logs for tracing

To find the journal output for traceback, run the following commands:
```
$ journalctl | grep osbuild
```

To show both remote or local workers:
```
$ journalctl -u osbuild-worker*
```

To show the running services:
```
$ journalctl -u osbuild-composer.service
```

### How to install and enable EPEL

https://www.redhat.com/sysadmin/install-epel-linux

# Things image builder can do that other methods can't

- Partitioning
- pre-load configuration so that a system is *NEVER* booted with unacceptable configurations (e.g. not yet hardened)
- Enable services at first boot
- Add more repos and install software from those repos

We are shooting for immutable infrastructure
