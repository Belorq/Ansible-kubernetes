# Ansible-kubernetes

**ОС:** Ubuntu 24.04.3 LTS

* Kubernetes v1.33.5
* calico
* nodelocaldns
* containerd

## Установка ansible

```bash
python3 -m venv venv
source venv/bin/activate
pip3 install ansible-core==2.19.2

ansible-galaxy collection install community.general
ansible-galaxy collection install ansible.posix
```

Генерируем ssh ключ:

```bash
ssh-keygen -t ed25519
```

Копируем в виртуальные машины из [inventory.ini](./inventory.ini)

```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub belor@192.168.122.151
ssh-copy-id -i ~/.ssh/id_ed25519.pub belor@192.168.122.152
ssh-copy-id -i ~/.ssh/id_ed25519.pub belor@192.168.122.153
```

Добавим на всех хостах нашего пользователя в sudoers

`ansible all -m ansible.builtin.lineinfile -a "path=/etc/sudoers line='belor ALL=(ALL) NOPASSWD:ALL' state=present validate='visudo -cf %s'" -b -K`

  ## Установка
  ```bash
ansible-playbook install-cluster.yml -b
```

  ## Tree
  ```conf
.
├── ansible.cfg
├── group_vars
│   └── k8s_cluster
├── install-cluster.yml
├── inventory.ini
└── roles
    ├── master
    │   ├── tasks
    │   │   ├── calico.yml
    │   │   ├── dns.yml
    │   │   ├── main.yml
    │   │   └── utils.yml
    │   └── templates
    │       ├── calico-install.j2
    │       ├── kubeadm-config.j2
    │       └── nodelocaldns-daemonset.j2
    ├── prepare-hosts
    │   ├── files
    │   │   └── modules-kubernetes.conf
    │   ├── tasks
    │   │   ├── containerd.yml
    │   │   └── main.yml
    │   └── templates
    │       └── crictl.j2
    └── workers
        └── tasks
            ├── join.yml
            ├── main.yml
            └── prepare.yml
```
