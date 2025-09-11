# Master node

```bash
ssh alex@k8s-master.local
```

## Setup master node

## Flash OS image to SD card

- Download Raspberry Pi imager <https://www.raspberrypi.com/software/>
- Select Ubuntu Server 25.04
- Cmd+Shift+X to open advanced options
- Set hostname to `k8s-master`
- Enable SSH
- Set username: `alex`

## Start Pi

Boot the Pi and connect to it via ssh using the hostname

```bash
ssh alex@k8s-master.local
```

### Install K3s

Using configuration flags from <https://docs.k3s.io/installation/configuration>

```bash
curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="server --disable traefik --disable servicelb --disable-network-policy" sh -
```

### Export the kubeconfig

- From Pi:

  ```bash
  sudo k3s kubectl config view --raw
  ```

- Paste the value into a file on your local machine `~/.kube/homelab_config`
- Set the KUBECONFIG environment variable

  ```bash
  kubectl krew install konfig
  kubectl konfig import -s ~/.kube/homelab_config
  ```

- Replace the server address in the kubeconfig file with the local network IP of the Pi

  ```bash
  vim ~/.kube/config
  ```

  Edit the server line to:

  ```yaml
  server: https://k8s-master.local:6443
  ```

### Enable cgroup memory and cpuset

```bash
# 1. Open the cmdline.txt file
sudo vim /boot/firmware/cmdline.txt

#2. Add below into THE END of the current line
cgroup_enable=cpuset cgroup_memory=1 cgroup_enable=memory

# 3. Save the file and reboot
sudo cat /boot/firmware/cmdline.txt
sudo shutdown -r now
```

## References

- <https://medium.com/@stevenhoang/step-by-step-guide-installing-k3s-on-a-raspberry-pi-4-cluster-8c12243800b9>

## Troubleshooting

### Clock sync

Set NTP:
sudo vim /etc/systemd/timesyncd.conf

```bash
[Time]
NTP=ntp.ubuntu.com
```

### x509

Remove certificate-authority-data from cluster in kubeconfig file

```yaml
- cluster:
    # certificate-authority-data: REDACTED
    server: https://k8s-master.local:6443
    insecure-skip-tls-verify: true
  name: homela
```
