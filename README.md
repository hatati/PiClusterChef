# PiClusterChef
Cook up a k3s cluster with a single command

## Prerequisites

You need to have installed the Raspbian OS on you Raspberry Pies, connect them to your local network, give them a hostname and enable their SSH servers. There is an excellent guide by w3Schools on how to do this [here](https://www.w3schools.com/nodejs/nodejs_raspberrypi.asp).

### Ansible

```bash
ansible-galaxy collection install community.general
```

```bash
ansible-galaxy collection install kubernetes.core
```

## Setup

Run this to copy your SSH public key to the Raspberry pies:

> **Note**: Make sure you have generated an SSH key pair on your Ubuntu machine. You can check by running:
>
> ```bash
> ls ~/.ssh/id_*.pub
> ```
>
> If you don't see any files listed, you'll need to generate an SSH key pair. You can do this by running:
>
> ```bash
> ssh-keygen
>```
>
> Follow the prompts to generate a new key pair. Make sure to keep your private key secure and do not share it with others.

```bash
ssh-copy-id nermin@nerminmaster
ssh-copy-id nermin@nerminworker1
```

Initial k3s kubeconfig:

```yaml
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: ...
    server: https://127.0.0.1:6443
  name: default
contexts:
- context:
    cluster: default
    user: default
  name: default
current-context: default
kind: Config
preferences: {}
users:
- name: default
  user:
    client-certificate-data: ...
    client-key-data: ...
```

Replaced values:

```yaml
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: ...
    server: https://nerminmaster:6443
  name: nermin-cluster
contexts:
- context:
    cluster: nermin-cluster
    user: nermin-cluster-user
  name: nermin-cluster
current-context: nermin-cluster
kind: Config
preferences: {}
users:
- name: nermin-cluster-user
  user:
    client-certificate-data: ...
    client-key-data: ...
```

Merge kubeconfigs:

```bash
kubectl get nodes --kubeconfig k3sconfig
```
