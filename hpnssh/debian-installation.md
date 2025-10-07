# Adding the HPN-SSH Repository to Debian 

Adding the HPN-SSH Debian repository found at [https://download.opensuse.org/repositories/home:/rapier1/](https://download.opensuse.org/repositories/home:/rapier1/) is relatively straightforward. This process has been tested under Debian 12 and Debian 13. 

### 1. Manually add the package source
```
. /etc/os-release
suites="Debian_${VERSION_ID}"

sudo mkdir -p --mode=0755 /usr/share/keyrings
out=$(mktemp -u)
if curl -fsSL "https://download.opensuse.org/repositories/home:/rapier1/${suites}/Release.key" \
    | gpg --dearmor | sudo tee "$out" > /dev/null; then
    sudo \mv -f "$out" /usr/share/keyrings/rapier1-hpnssh.gpg
fi

cat << EOF | sudo tee /etc/apt/sources.list.d/hpnssh.sources
Types: deb
URIs: https://download.opensuse.org/repositories/home:/rapier1/${suites}/
Suites: /
Signed-By: /usr/share/keyrings/rapier1-hpnssh.gpg
EOF
```
### 2. Install the hpnssh-server package
```
sudo chmod 644 /usr/share/keyrings/rapier1-hpnssh.gpg /etc/apt/sources.list.d/hpnssh.sources \
    && sudo apt-get update -qq \
    && sudo apt-get install -y hpnssh-client hpnssh-server hpnssh-sftp-server

sudo systemctl enable --now hpnssh.service
systemctl is-enabled hpnssh.service
```

Documentation contributed by [IceCodeNew@github.com](mailto:IceCodeNew@github.com)

*Updated: October 2, 2025 by C. Rapier*
