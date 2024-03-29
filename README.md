# Ansible role [gitlab_ee](https://galaxy.ansible.com/ui/standalone/roles/buluma/gitlab_ee/documentation)

Ansible Role for GitLab EE Installation

|GitHub|Version|Issues|Pull Requests|Downloads|
|------|-------|------|-------------|---------|
|[![github](https://github.com/buluma/ansible-role-gitlab_ee/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/ansible-role-gitlab_ee/actions/workflows/molecule.yml)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-gitlab_ee.svg)](https://github.com/buluma/ansible-role-gitlab_ee/releases/)|[![Issues](https://img.shields.io/github/issues/buluma/ansible-role-gitlab_ee.svg)](https://github.com/buluma/ansible-role-gitlab_ee/issues/)|[![PullRequests](https://img.shields.io/github/issues-pr-closed-raw/buluma/ansible-role-gitlab_ee.svg)](https://github.com/buluma/ansible-role-gitlab_ee/pulls/)|[![Ansible Role](https://img.shields.io/ansible/role/d/buluma/gitlab_ee)](https://galaxy.ansible.com/ui/standalone/roles/buluma/gitlab_ee/documentation)|

## [Example Playbook](#example-playbook)

This example is taken from [`molecule/default/converge.yml`](https://github.com/buluma/ansible-role-gitlab_ee/blob/master/molecule/default/converge.yml) and is tested on each push, pull request and release.

```yaml
---
- name: Converge
  hosts: all
  remote_user: root
  become: true
  gather_facts: yes
  roles:
    - role: buluma.bootstrap
    - role: buluma.setuptools
    - role: buluma.timezone
    - role: buluma.gitlab_ee
```

The machine needs to be prepared. In CI this is done using [`molecule/default/prepare.yml`](https://github.com/buluma/ansible-role-gitlab_ee/blob/master/molecule/default/prepare.yml):

```yaml
---
- name: Prepare
  hosts: all
  remote_user: root
  become: true
  gather_facts: false
  tasks:
    - name: Redhat | subscription-manager register
      ansible.builtin.raw: |
        set -eu
        subscription-manager register \
          --username={{ lookup('env', 'REDHAT_USERNAME') }} \
          --password={{ lookup('env', 'REDHAT_PASSWORD') }} \
          --auto-attach
      changed_when: false
      failed_when: false

    - name: Debian | apt-get install python3
      ansible.builtin.raw: |
        set -eu
        apt-get update
        DEBIAN_FRONTEND=noninteractive apt-get install -y python3
      changed_when: false
      failed_when: false

    - name: Redhat | yum install python3
      ansible.builtin.raw: |
        set -eu
        yum makecache
        yum install -y python3
      changed_when: false
      failed_when: false

    - name: Suse | zypper install python3
      ansible.builtin.raw: |
        set -eu
        zypper -n --gpg-auto-import-keys refresh
        zypper -n install -y python3
      changed_when: false
      failed_when: false

    - name: Cp -rfT /etc/skel /root
      ansible.builtin.raw: |
        set -eu
        cp -rfT /etc/skel /root
      changed_when: false
      failed_when: false

    - name: Setenforce 0
      ansible.builtin.raw: |
        set -eu
        setenforce 0
        sed -i 's/^SELINUX=.*$/SELINUX=permissive/g' /etc/selinux/config
      changed_when: false
      failed_when: false

    - name: Systemctl stop firewalld.service
      ansible.builtin.raw: |
        set -eu
        systemctl stop firewalld.service
        systemctl disable firewalld.service
      changed_when: false
      failed_when: false

    - name: Systemctl stop ufw.service
      ansible.builtin.raw: |
        set -eu
        systemctl stop ufw.service
        systemctl disable ufw.service
      changed_when: false
      failed_when: false

    - name: Debian | apt-get install *.deb
      ansible.builtin.raw: |
        set -eu
        DEBIAN_FRONTEND=noninteractive apt-get install -y bzip2 ca-certificates curl gcc gnupg gzip hostname iproute2 passwd procps python3 python3-apt python3-jmespath python3-lxml python3-pip python3-setuptools python3-venv python3-virtualenv python3-wheel rsync sudo tar unzip util-linux xz-utils zip
      when: ansible_os_family | lower == "debian"
      changed_when: false
      failed_when: false

    - name: Fedora | yum install *.rpm
      ansible.builtin.raw: |
        set -eu
        yum install -y bzip2 ca-certificates curl gcc gnupg2 gzip hostname iproute procps-ng python3 python3-dnf-plugin-versionlock python3-jmespath python3-libselinux python3-lxml python3-pip python3-setuptools python3-virtualenv python3-wheel rsync shadow-utils sudo tar unzip util-linux xz yum-utils zip
      when: ansible_distribution | lower == "fedora"
      changed_when: false
      failed_when: false

    - name: Redhat-9 | yum install *.rpm
      ansible.builtin.raw: |
        set -eu
        yum-config-manager --enable crb || echo $?
        yum-config-manager --enable codeready-builder-beta-for-rhel-9-x86_64-rpms || echo $?
        yum install -y http://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm
        yum install -y bzip2 ca-certificates curl gcc gnupg2 gzip hostname iproute procps-ng python3 python3-dnf-plugin-versionlock python3-jmespath python3-libselinux python3-lxml python3-pip python3-setuptools python3-virtualenv python3-wheel rsync shadow-utils sudo tar unzip util-linux xz yum-utils zip
      when: ansible_os_family | lower == "redhat" and ansible_distribution_major_version | lower == "9"
      changed_when: false
      failed_when: false

    - name: Redhat-8 | yum install *.rpm
      ansible.builtin.raw: |
        set -eu
        yum install -y http://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
        yum install -y bzip2 ca-certificates curl gcc gnupg2 gzip hostname iproute procps-ng python3 python3-dnf-plugin-versionlock python3-jmespath python3-libselinux python3-lxml python3-pip python3-setuptools python3-virtualenv python3-wheel rsync shadow-utils sudo tar unzip util-linux xz yum-utils zip
      when: ansible_os_family | lower == "redhat" and ansible_distribution_major_version | lower == "8"
      changed_when: false
      failed_when: false

    - name: Redhat-7 | yum install *.rpm
      ansible.builtin.raw: |
        set -eu
        subscription-manager repos --enable=rhel-7-server-optional-rpms || echo $?
        yum install -y http://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
        yum install -y bzip2 ca-certificates curl gcc gnupg2 gzip hostname iproute procps-ng python3 python3-jmespath python3-libselinux python3-lxml python3-pip python3-setuptools python3-virtualenv python3-wheel rsync shadow-utils sudo tar unzip util-linux xz yum-plugin-versionlock yum-utils zip
      when: ansible_os_family | lower == "redhat" and ansible_distribution_major_version | lower == "7"
      changed_when: false
      failed_when: false

    - name: Suse | zypper -n install *.rpm
      ansible.builtin.raw: |
        set -eu
        zypper -n install -y bzip2 ca-certificates curl gcc gpg2 gzip hostname iproute2 procps python3 python3-jmespath python3-lxml python3-pip python3-setuptools python3-virtualenv python3-wheel rsync shadow sudo tar unzip util-linux xz zip
      when: ansible_os_family | lower == "suse"
      changed_when: false
      failed_when: false
```

Also see a [full explanation and example](https://buluma.github.io/how-to-use-these-roles.html) on how to use these roles.

## [Role Variables](#role-variables)

The default values for the variables are set in [`defaults/main.yml`](https://github.com/buluma/ansible-role-gitlab_ee/blob/master/defaults/main.yml):

```yaml
---
# GitLab release.
gitlab_release: "14.9"

# GitLab version.
gitlab_version: "{{ _gitlab_version[gitlab_release] }}"

# GitLab external URL.
gitlab_external_url: "http://{{ ansible_default_ipv4.address }}"

# Set to approximately 1/4 of available RAM.
gitlab_postgresql_shared_buffers: "256MB"

# Disable Prometheus node_exporter inside Docker.
gitlab_node_exporter_enable: "true"

# Manage accounts with docker.
gitlab_manage_accounts_enable: "false"

# Explicitly disable init detection since we are running on a container.
gitlab_package_detect_init: "true"

# Attempt to modify kernel paramaters.
gitlab_package_modify_kernel_parameters: "true"
```

## [Requirements](#requirements)

- pip packages listed in [requirements.txt](https://github.com/buluma/ansible-role-gitlab_ee/blob/master/requirements.txt).

## [State of used roles](#state-of-used-roles)

The following roles are used to prepare a system. You can prepare your system in another way.

| Requirement | GitHub | Version |
|-------------|--------|--------|
|[buluma.bootstrap](https://galaxy.ansible.com/buluma/bootstrap)|[![Ansible Molecule](https://github.com/buluma/ansible-role-bootstrap/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/ansible-role-bootstrap/actions/workflows/molecule.yml)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-bootstrap.svg)](https://github.com/shadowwalker/ansible-role-bootstrap)|
|[buluma.setuptools](https://galaxy.ansible.com/buluma/setuptools)|[![Ansible Molecule](https://github.com/buluma/ansible-role-setuptools/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/ansible-role-setuptools/actions/workflows/molecule.yml)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-setuptools.svg)](https://github.com/shadowwalker/ansible-role-setuptools)|
|[buluma.timezone](https://galaxy.ansible.com/buluma/timezone)|[![Ansible Molecule](https://github.com/buluma/ansible-role-timezone/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/ansible-role-timezone/actions/workflows/molecule.yml)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-timezone.svg)](https://github.com/shadowwalker/ansible-role-timezone)|

## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://buluma.github.io/) for further information.

Here is an overview of related roles:

![dependencies](https://raw.githubusercontent.com/buluma/ansible-role-gitlab_ee/png/requirements.png "Dependencies")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/buluma):

|container|tags|
|---------|----|
|[Ubuntu](https://hub.docker.com/repository/docker/buluma/ubuntu/general)|all|
|[EL](https://hub.docker.com/repository/docker/buluma/enterpriselinux/general)|all|
|[Debian](https://hub.docker.com/repository/docker/buluma/debian/general)|all|
|[Fedora](https://hub.docker.com/repository/docker/buluma/fedora/general)|all|
|[opensuse](https://hub.docker.com/repository/docker/buluma/opensuse/general)|all|

The minimum version of Ansible required is 2.12, tests have been done to:

- The previous version.
- The current version.
- The development version.

If you find issues, please register them in [GitHub](https://github.com/buluma/ansible-role-gitlab_ee/issues)

## [Changelog](#changelog)

[Role History](https://github.com/buluma/ansible-role-gitlab_ee/blob/master/CHANGELOG.md)

## [License](#license)

[Apache-2.0](https://github.com/buluma/ansible-role-gitlab_ee/blob/master/LICENSE)

## [Author Information](#author-information)

[Shadow Walker](https://buluma.github.io/)

