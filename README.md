# Ansible role: labocbz.install_redis

![Licence Status](https://img.shields.io/badge/licence-MIT-brightgreen)
![CI Status](https://img.shields.io/badge/CI-success-brightgreen)
![Testing Method](https://img.shields.io/badge/Testing%20Method-Ansible%20Molecule-blueviolet)
![Testing Driver](https://img.shields.io/badge/Testing%20Driver-docker-blueviolet)
![Language Status](https://img.shields.io/badge/language-Ansible-red)
![Compagny](https://img.shields.io/badge/Compagny-Labo--CBZ-blue)
![Author](https://img.shields.io/badge/Author-Lord%20Robin%20Crombez-blue)

## Description

![Tag: Ansible](https://img.shields.io/badge/Tech-Ansible-orange)
![Tag: Debian](https://img.shields.io/badge/Tech-Debian-orange)
![Tag: Redis](https://img.shields.io/badge/Tech-Redis-orange)
![Tag: Standalone](https://img.shields.io/badge/Tech-Standalone-orange)
![Tag: Cluster](https://img.shields.io/badge/Tech-Cluster-orange)

An Ansible role to install and configure a Redis server on your host.

The Ansible role installs Redis on a target system and offers additional features to facilitate the deployment of Redis as a service. The role supports the installation and execution of Redis in service mode. Additionally, if you want to set up a Redis cluster, it provides the flexibility to create multiple Redis services based on the chosen port number, for example, redis@6378. This enables running multiple Redis instances on a single machine, making it feasible to host three Redis servers on three individual machines, ultimately achieving the required nine Redis servers (3 masters + 2 replicas each).

In the context of a Redis cluster, to establish three master nodes, you would need a total of nine servers, taking into account both master and replica nodes. However, this configuration can be costly due to the number of required servers. To address this concern, the role proposes an alternative approach, allowing the creation of multiple Redis services on a single machine, each identified by a unique port number. By doing so, you can distribute the Redis services across fewer physical servers while still achieving the desired nine Redis servers.

The role's configurability is achieved through variables, enabling you to define the desired Redis configuration, including the bind address, port, protected mode, log level, and authentication settings. You can also set up SSL support with custom SSL certificate paths. Additionally, the role provides options for Redis cluster-related settings, such as specifying the number of replicas and whether to bootstrap the cluster.

In summary, the Redis role simplifies the installation and deployment of Redis, offering the flexibility to create Redis services with customizable settings, facilitating the setup of Redis clusters while optimizing resource utilization. Its rich configuration options empower administrators to tailor Redis deployments to their specific requirements, providing a robust solution for efficient and scalable Redis infrastructures.

## Folder structure

By default Ansible will look in each directory within a role for a main.yml file for relevant content (also man.yml and main):

```PYTHON
.
├── README.md  # Contains an overview of the role and its purpose.
├── defaults
│   ├── main.yml  # Contains default variables for the role that can be overridden by users.
│   └── README.md  # Contains documentation for the default variables.
├── files
│   └── README.md  # Contains documentation for the files in the directory.
├── handlers
│   ├── main.yml  # Contains handlers that can be called by tasks within the role.
│   └── README.md  # Contains documentation for the handlers.
├── meta
│   ├── main.yml  # Contains metadata about the role, including dependencies and supported platforms.
│   └── README.md  # Contains documentation for the role metadata.
├── tasks
│   ├── main.yml  # Contains tasks to be executed by the role on the managed nodes.
│   └── README.md  # Contains documentation for the tasks.
├── templates
│   └── README.md  # Contains documentation for the templates.
└── vars
    ├── main.yml  # Contains variables that are specific to the role and are not meant to be overridden.
    └── README.md  # Contains documentation for the role variables.
```

## Tests and simulations

### Basics

You have to run multiples tests. *tests with an # are mandatory*

```MARKDOWN
# lint
# syntax
# converge
# idempotence
# verify
side_effect
```

Executing theses test in this order is called a "scenario" and Molecule can handle them.

Molecule use Ansible and pre configured playbook to create containers, prepare them, converge (run the role) and verify its execution.
You can manage multiples scenario with multiples tests in order to get a 100% code coverage.

This role contains a ./tests folder. In this folder you can use the inventory or the tower folder to create a simualtion of a real inventory and a real AWX / Tower job execution.

### Command reminder

```SHELL
# Check your YAML syntax
yamllint -c ./.yamllint .

# Check your Ansible syntax and code security
ansible-lint --config=./.ansible-lint .

# Execute and test your role
molecule lint
molecule create
molecule list
molecule converge
molecule verify
molecule destroy

# Execute all previous task in one single command
molecule test
```

## Installation

To install this role, just copy/import this role or raw file into your fresh playbook repository or call it with the "include_role/import_role" module.

## Usage

### Vars

Some vars a required to run this role:

```YAML
---
redis_bind: "0.0.0.0"
redis_port: 6379
redis_proteced_mode: "no"
redis_loglevel: "notice"
redis_requirepass: "mySecret"

redis_cluster: false
redis_cluster_replicas: 2
redis_cluster_bootstrap: true

redis_lib_path: "/var/lib/redis/redis-server-{{ redis_port }}"
redis_run_path: "/var/run/redis/redis-server-{{ redis_port }}"
redis_log_path: "/var/log/redis/redis-server-{{ redis_port }}"

redis_dir: "{{ redis_lib_path }}"
redis_logfile: "{{ redis_log_path }}/redis-server-{{ redis_port }}.log"
redis_path: "/etc/redis/redis-server-{{ redis_port }}"
redis_conf: "{{ redis_path }}/redis.conf"

redis_ssl: true
redis_ssl_domain: "my.redis-cluster.domain.tld"
redis_ssl_path: "/etc/redis/ssl"
redis_ssl_key: "{{ redis_ssl_path }}/{{ redis_ssl_domain }}/{{ redis_ssl_domain }}.key"
redis_ssl_cert: "{{ redis_ssl_path }}/{{ redis_ssl_domain }}/{{ redis_ssl_domain }}.crt"
redis_ssl_ca: "/etc/ssl/cacerts"
redis_ssl_auth_client: no

```

The best way is to modify these vars by copy the ./default/main.yml file into the ./vars and edit with your personnals requirements.

You can set vars in the template model in Ansible AWX / Tower or just surchage them during the playbook call.

In order to surchage vars, you have multiples possibilities but for mains cases you have to put vars in your inventory and/or on your AWX / Tower interface.

```YAML
# From inventory
---
inv_redis_bind: "0.0.0.0"
inv_redis_proteced_mode: "no"
inv_redis_loglevel: "notice"
inv_redis_requirepass: "mySecret"

inv_redis_cluster: true
inv_redis_cluster_replicas: 2
inv_redis_cluster_bootstrap: true

inv_redis_path: "/etc/redis/redis-server-{{ port }}"
inv_redis_conf: "{{ inv_redis_path }}/redis.conf"

inv_redis_ports:
  - 6379
  - 6380
  - 6381

inv_redis_ssl: true
inv_redis_ssl_domain: "my-redis-cluster.domain.tld"
inv_redis_ssl_path: "/etc/redis/ssl"
inv_redis_ssl_key: "{{ inv_redis_ssl_path }}/{{ inv_redis_ssl_domain }}/{{ inv_redis_ssl_domain }}.pem.key"
inv_redis_ssl_cert: "{{ inv_redis_ssl_path }}/{{ inv_redis_ssl_domain }}/{{ inv_redis_ssl_domain }}.pem.crt"
inv_redis_ssl_ca: "{{ inv_redis_ssl_path }}/{{ inv_redis_ssl_domain }}/ca-chain.pem.crt"
inv_redis_ssl_auth_client: "no"

```

```YAML
# From AWX / Tower
---

```

### Run

To run this role, you can copy the molecule/default/converge.yml playbook and add it into your playbook:

```YAML
- name: "Include labocbz.install_redis"
  tags:
    - "labocbz.install_redis"
  loop: "{{ inv_redis_ports }}"
  loop_control:
    loop_var: port
  vars:
    redis_bind: "{{ inv_redis_bind }}"
    redis_port: "{{ port }}"
    redis_proteced_mode: "{{ inv_redis_proteced_mode }}"
    redis_loglevel: "{{ inv_redis_loglevel }}"
    redis_requirepass: "{{ inv_redis_requirepass }}"
    redis_cluster: "{{ inv_redis_cluster }}"
    redis_path: "{{ inv_redis_path }}"
    redis_conf: "{{ inv_redis_conf }}"
    redis_ssl: "{{ inv_redis_ssl }}"
    redis_ssl_domain: "{{ inv_redis_ssl_domain }}"
    redis_ssl_path: "{{ inv_redis_ssl_path }}"
    redis_ssl_key: "{{ inv_redis_ssl_key }}"
    redis_ssl_cert: "{{ inv_redis_ssl_cert }}"
    redis_ssl_ca: "{{ inv_redis_ssl_ca }}"
    redis_ssl_auth_client: "{{ inv_redis_ssl_auth_client }}"
    redis_cluster_replicas: "{{ inv_redis_cluster_replicas }}"
    redis_cluster_bootstrap: "{{ inv_redis_cluster_bootstrap }}"
  ansible.builtin.include_role:
    name: "labocbz.install_redis"
```

You can run this command to enable the cluster on one node, with for example 3 nodes/serveur with 3 services on each:

```SHELL
# Enable the cluster
redis-cli -a 'mySecret' --tls --no-auth-warning --cluster create \
172.17.0.3:6379 \
172.17.0.4:6379 \
172.17.0.5:6379 \
172.17.0.3:6380 \
172.17.0.4:6380 \
172.17.0.5:6380 \
172.17.0.3:6381 \
172.17.0.4:6381 \
172.17.0.5:6381 \
--cluster-replicas 2 \
--cluster-yes \
--cert /etc/redis/ssl/my-redis-cluster.domain.tld/my-redis-cluster.domain.tld.pem.crt \
--key /etc/redis/ssl/my-redis-cluster.domain.tld/my-redis-cluster.domain.tld.pem.key
# --cacert /usr/local/share/ca-certificates/My-Local-CA-Authority/My-Local-CA-Authority.crt \
# Check activation
redis-cli -a 'mySecret' --tls --no-auth-warning  \
--cert /etc/redis/ssl/my.redis-cluster.domain.tld/my.redis-cluster.domain.tld.crt \
--key /etc/redis/ssl/my.redis-cluster.domain.tld/my.redis-cluster.domain.tld.key \
-h 172.17.0.3 -p 6379 cluster nodes
# --cacert /usr/local/share/ca-certificates/My-Local-CA-Authority/My-Local-CA-Authority.crt \

```

## Architectural Decisions Records

Here you can put your change to keep a trace of your work and decisions.

### 2023-05-15: First Init

* First init of this role with the bootstrap_role playbook by Lord Robin Crombez
* Enabled SSL/TLS, but disable it after testing clustering ... (join cluster hangs up)
* A group "redis" can be use to set a group of ports, in order to install multiple Redis instance of the same host
* Clustering is possible by create (1 master + 2 replicas)x N, so if 3 servers are available for a Redis cluster, we have to install 3 Redis instance on it.

### 2023-05-30: Cryptographic update

* SSL/TLS Materials are not handled by the role
* Certs/CA have to be installed previously/after this role use

### 2023-10-06: New CICD, new Images

* New CI/CD scenario name
* Molecule now use remote Docker image by Lord Robin Crombez
* Molecule now use custom Docker image in CI/CD by env vars
* New CICD with needs and optimization

## Authors

* Lord Robin Crombez

## Sources

* [Ansible role documentation](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html)
* [Ansible Molecule documentation](https://molecule.readthedocs.io/)
* [Redis over SSL/TLS](https://www.server-world.info/en/note?os=Ubuntu_22.04&p=redis6&f=4)
* [I am trying to enable TLS for Redis cluster](https://groups.google.com/g/redis-db/c/RxNuJX-d65A)
* [Clustering in Redis](https://www.youtube.com/watch?v=3WOfXRjYnGA)
* [How to Create a Cluster in Redis](https://www.youtube.com/watch?v=N8BkmdZzxDg)
* [creating_a_redis_cluster.md](https://gist.github.com/justincastilla/7bbff2fdab5bd039aaee5e2f725dab80)
* [Redis via ssl and nc 21.0.3](https://help.nextcloud.com/t/redis-via-ssl-and-nc-21-0-3/119656)
* [Can’t open the log file: Read-only file system](https://rtfm.co.ua/en/redis-cant-open-the-log-file-read-only-file-system-2/)
* [redis service fails with permission denied on append file](https://stackoverflow.com/questions/55201167/redis-service-fails-with-permission-denied-on-append-file)
* [Redis cluster specification](https://redis.io/docs/reference/cluster-spec/)
* [Tuto Redis : Le cluster redis 3.0](https://www.wanadev.fr/30-tuto-redis-le-cluster-redis-3-0/)
* [How to set up a Redis Cluster with TLS in a local machine](https://www.dltlabs.com/blog/how-to-set-up-a-redis-cluster-with-tls-in-a-local-machine--679616)
* [CLUSTER MEET doesn't allow hostname to be used](https://github.com/redis/redis/issues/2410)
* [Set up a Redis Cluster for Production environments](https://success.outsystems.com/documentation/how_to_guides/infrastructure/configuring_outsystems_with_redis_in_memory_session_storage/set_up_a_redis_cluster_for_production_environments/)
