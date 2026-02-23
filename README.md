# Домашнее задание к занятию 5 «Тестирование roles» - Лебедев В.В. FOPS-33

## Подготовка к выполнению

1. Установите molecule и его драйвера: `pip3 install "molecule molecule_docker molecule_podman`.
2. Выполните `docker pull aragast/netology:latest` —  это образ с podman, tox и несколькими пайтонами (3.7 и 3.9) внутри.

## Основная часть

Ваша цель — настроить тестирование ваших ролей. 

Задача — сделать сценарии тестирования для vector. 

Ожидаемый результат — все сценарии успешно проходят тестирование ролей.

### Molecule

1. Запустите  `molecule test -s ubuntu_xenial` (или с любым другим сценарием, не имеет значения) внутри корневой директории clickhouse-role, посмотрите на вывод команды. Данная команда может отработать с ошибками или не отработать вовсе, это нормально. Наша цель - посмотреть как другие в реальном мире используют молекулу И из чего может состоять сценарий тестирования.
2. Перейдите в каталог с ролью vector-role и создайте сценарий тестирования по умолчанию при помощи `molecule init scenario --driver-name docker`.
3. Добавьте несколько разных дистрибутивов (oraclelinux:8, ubuntu:latest) для инстансов и протестируйте роль, исправьте найденные ошибки, если они есть.
4. Добавьте несколько assert в verify.yml-файл для  проверки работоспособности vector-role (проверка, что конфиг валидный, проверка успешности запуска и др.). 
5. Запустите тестирование роли повторно и проверьте, что оно прошло успешно.
5. Добавьте новый тег на коммит с рабочим сценарием в соответствии с семантическим версионированием.

### Решение Molecule

1. Получаем ошибку из за отсутствия сценариев
![img1](img/img1.jpg)
2. В новых версиях molecule драйвер не задается через экстра параметры в командной строке. Выполняем для инициализации
`molecule init scenario`
![img1-2](img/img1-2.jpg)
3. Добавляем в драйвер docker а в платформы несколько дистрибутивов
```yml 
# default/molecule.yml
---
dependency:
  name: galaxy
driver:
  name: docker
platforms:
  - name: ubuntu-vector
    image: docker.io/pycontribs/ubuntu:latest
    pre_build_image: true
  - name: centos-7-vector
    image: docker.io/pycontribs/centos:7
    pre_build_image: true
  - name: fedora-vector
    image: docker.io/pycontribs/fedora:latest
    pre_build_image: true
provisioner:
  name: ansible
  playbooks:
    converge: converge.yml
    verify: verify.yml
verifier:
  name: ansible
```
4. Выполняем `molecule test -s default`

<details>


<summary>результат выполнения команды: molecule test -s default </summary>

```shell 
INFO     default ➜ discovery: scenario test matrix: dependency, cleanup, destroy, syntax, create, prepare, converge, idempotence, side_effect, verify, cleanup, destroy
INFO     default ➜ prerun: Performing prerun with role_name_check=0...
INFO     default ➜ dependency: Executing
WARNING  default ➜ dependency: Missing roles requirements file: requirements.yml
WARNING  default ➜ dependency: Missing collections requirements file: collections.yml
WARNING  default ➜ dependency: Executed: 2 missing (Remove from test_sequence to suppress)
INFO     default ➜ cleanup: Executing
WARNING  default ➜ cleanup: Executed: Missing playbook (Remove from test_sequence to suppress)
INFO     default ➜ destroy: Executing
INFO     Sanity checks: 'docker'

PLAY [Destroy] *****************************************************************

TASK [Destroy molecule instance(s)] ********************************************
ok: [localhost] => (item={'image': 'docker.io/pycontribs/ubuntu:latest', 'name': 'ubuntu-vector', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'docker.io/pycontribs/centos:7', 'name': 'centos-7-vector', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'docker.io/pycontribs/fedora:latest', 'name': 'fedora-vector', 'pre_build_image': True})

PLAY RECAP *********************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     default ➜ destroy: Executed: Successful
INFO     default ➜ syntax: Executing

playbook: /home/viktor/08-ansible-05-testing-hw/roles/vector-role/molecule/default/converge.yml
INFO     default ➜ syntax: Executed: Successful
INFO     default ➜ create: Executing

PLAY [Create] ******************************************************************

TASK [Set async_dir for HOME env] **********************************************
ok: [localhost]

TASK [Log into a Docker registry] **********************************************
skipping: [localhost] => (item=None)
skipping: [localhost] => (item=None)
skipping: [localhost] => (item=None)
skipping: [localhost]

TASK [Check presence of custom Dockerfiles] ************************************
ok: [localhost] => (item={'image': 'docker.io/pycontribs/ubuntu:latest', 'name': 'ubuntu-vector', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'docker.io/pycontribs/centos:7', 'name': 'centos-7-vector', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'docker.io/pycontribs/fedora:latest', 'name': 'fedora-vector', 'pre_build_image': True})

TASK [Create Dockerfiles from image names] *************************************
skipping: [localhost] => (item={'image': 'docker.io/pycontribs/ubuntu:latest', 'name': 'ubuntu-vector', 'pre_build_image': True})
skipping: [localhost] => (item={'image': 'docker.io/pycontribs/centos:7', 'name': 'centos-7-vector', 'pre_build_image': True})
skipping: [localhost] => (item={'image': 'docker.io/pycontribs/fedora:latest', 'name': 'fedora-vector', 'pre_build_image': True})
skipping: [localhost]

TASK [Synchronization the context] *********************************************
skipping: [localhost] => (item={'image': 'docker.io/pycontribs/ubuntu:latest', 'name': 'ubuntu-vector', 'pre_build_image': True})
skipping: [localhost] => (item={'image': 'docker.io/pycontribs/centos:7', 'name': 'centos-7-vector', 'pre_build_image': True})
skipping: [localhost] => (item={'image': 'docker.io/pycontribs/fedora:latest', 'name': 'fedora-vector', 'pre_build_image': True})
skipping: [localhost]

TASK [Discover local Docker images] ********************************************
ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'false_condition': 'not item.pre_build_image | default(false)', 'item': {'image': 'docker.io/pycontribs/ubuntu:latest', 'name': 'ubuntu-vector', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 0, 'ansible_index_var': 'i'})
ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'false_condition': 'not item.pre_build_image | default(false)', 'item': {'image': 'docker.io/pycontribs/centos:7', 'name': 'centos-7-vector', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 1, 'ansible_index_var': 'i'})
ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'false_condition': 'not item.pre_build_image | default(false)', 'item': {'image': 'docker.io/pycontribs/fedora:latest', 'name': 'fedora-vector', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 2, 'ansible_index_var': 'i'})

TASK [Build an Ansible compatible image (new)] *********************************
skipping: [localhost] => (item=molecule_local/docker.io/pycontribs/ubuntu:latest)
skipping: [localhost] => (item=molecule_local/docker.io/pycontribs/centos:7)
skipping: [localhost] => (item=molecule_local/docker.io/pycontribs/fedora:latest)
skipping: [localhost]

TASK [Create docker network(s)] ************************************************
skipping: [localhost]

TASK [Determine the CMD directives] ********************************************
ok: [localhost] => (item={'image': 'docker.io/pycontribs/ubuntu:latest', 'name': 'ubuntu-vector', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'docker.io/pycontribs/centos:7', 'name': 'centos-7-vector', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'docker.io/pycontribs/fedora:latest', 'name': 'fedora-vector', 'pre_build_image': True})

TASK [Create molecule instance(s)] *********************************************
changed: [localhost] => (item=ubuntu-vector)
changed: [localhost] => (item=centos-7-vector)
changed: [localhost] => (item=fedora-vector)

TASK [Wait for instance(s) creation to complete] *******************************
FAILED - RETRYING: [localhost]: Wait for instance(s) creation to complete (300 retries left).
changed: [localhost] => (item={'failed': 0, 'started': 1, 'finished': 0, 'ansible_job_id': 'j587967282895.54092', 'results_file': '/home/viktor/.ansible_async/j587967282895.54092', 'changed': True, 'item': {'image': 'docker.io/pycontribs/ubuntu:latest', 'name': 'ubuntu-vector', 'pre_build_image': True}, 'ansible_loop_var': 'item'})
changed: [localhost] => (item={'failed': 0, 'started': 1, 'finished': 0, 'ansible_job_id': 'j991267067653.54117', 'results_file': '/home/viktor/.ansible_async/j991267067653.54117', 'changed': True, 'item': {'image': 'docker.io/pycontribs/centos:7', 'name': 'centos-7-vector', 'pre_build_image': True}, 'ansible_loop_var': 'item'})
changed: [localhost] => (item={'failed': 0, 'started': 1, 'finished': 0, 'ansible_job_id': 'j436313306975.54142', 'results_file': '/home/viktor/.ansible_async/j436313306975.54142', 'changed': True, 'item': {'image': 'docker.io/pycontribs/fedora:latest', 'name': 'fedora-vector', 'pre_build_image': True}, 'ansible_loop_var': 'item'})

PLAY RECAP *********************************************************************
localhost                  : ok=6    changed=2    unreachable=0    failed=0    skipped=5    rescued=0    ignored=0

INFO     default ➜ create: Executed: Successful
INFO     default ➜ prepare: Executing
WARNING  default ➜ prepare: Executed: Missing playbook (Remove from test_sequence to suppress)
INFO     default ➜ converge: Executing

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] *********************************************************
ok: [fedora-vector]
ok: [ubuntu-vector]
ok: [centos-7-vector]

TASK [Include install tasks directly] ******************************************
included: /home/viktor/08-ansible-05-testing-hw/roles/vector-role/tasks/install_vector.yml for centos-7-vector, fedora-vector, ubuntu-vector

TASK [Get OS family] ***********************************************************
ok: [centos-7-vector] => {
    "ansible_os_family": "RedHat"
}
ok: [fedora-vector] => {
    "ansible_os_family": "RedHat"
}
ok: [ubuntu-vector] => {
    "ansible_os_family": "Debian"
}

TASK [Download Vector deb package] *********************************************
skipping: [centos-7-vector]
skipping: [fedora-vector]
changed: [ubuntu-vector]

TASK [Install Vector deb package] **********************************************
skipping: [centos-7-vector]
skipping: [fedora-vector]
changed: [ubuntu-vector]

TASK [Download Vector rpm package] *********************************************
skipping: [fedora-vector]
skipping: [ubuntu-vector]
changed: [centos-7-vector]

TASK [Install Vector rpm package] **********************************************
skipping: [fedora-vector]
skipping: [ubuntu-vector]
changed: [centos-7-vector]

TASK [Download Vector rpm package] *********************************************
skipping: [centos-7-vector]
skipping: [ubuntu-vector]
changed: [fedora-vector]

TASK [Install Vector rpm package with dnf] *************************************
skipping: [centos-7-vector]
skipping: [ubuntu-vector]
changed: [fedora-vector]

TASK [Create vector config directory] ******************************************
ok: [centos-7-vector]
ok: [ubuntu-vector]
ok: [fedora-vector]

TASK [Create simple vector config for testing] *********************************
changed: [centos-7-vector]
changed: [ubuntu-vector]
changed: [fedora-vector]

TASK [Verify vector installation] **********************************************
ok: [ubuntu-vector]
ok: [fedora-vector]
ok: [centos-7-vector]

TASK [Display vector version] **************************************************
ok: [centos-7-vector] => {
    "msg": "Vector version: vector 0.21.0 (x86_64-unknown-linux-gnu c1edb89 2022-04-14)"
}
ok: [fedora-vector] => {
    "msg": "Vector version: vector 0.21.0 (x86_64-unknown-linux-gnu c1edb89 2022-04-14)"
}
ok: [ubuntu-vector] => {
    "msg": "Vector version: vector 0.21.0 (x86_64-unknown-linux-gnu c1edb89 2022-04-14)"
}

PLAY RECAP *********************************************************************
centos-7-vector            : ok=9    changed=3    unreachable=0    failed=0    skipped=4    rescued=0    ignored=0
fedora-vector              : ok=9    changed=3    unreachable=0    failed=0    skipped=4    rescued=0    ignored=0
ubuntu-vector              : ok=9    changed=3    unreachable=0    failed=0    skipped=4    rescued=0    ignored=0

INFO     default ➜ converge: Executed: Successful
INFO     default ➜ idempotence: Executing

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] *********************************************************
ok: [fedora-vector]
ok: [ubuntu-vector]
ok: [centos-7-vector]

TASK [Include install tasks directly] ******************************************
included: /home/viktor/08-ansible-05-testing-hw/roles/vector-role/tasks/install_vector.yml for centos-7-vector, fedora-vector, ubuntu-vector

TASK [Get OS family] ***********************************************************
ok: [centos-7-vector] => {
    "ansible_os_family": "RedHat"
}
ok: [fedora-vector] => {
    "ansible_os_family": "RedHat"
}
ok: [ubuntu-vector] => {
    "ansible_os_family": "Debian"
}

TASK [Download Vector deb package] *********************************************
skipping: [centos-7-vector]
skipping: [fedora-vector]
ok: [ubuntu-vector]

TASK [Install Vector deb package] **********************************************
skipping: [centos-7-vector]
skipping: [fedora-vector]
ok: [ubuntu-vector]

TASK [Download Vector rpm package] *********************************************
skipping: [fedora-vector]
skipping: [ubuntu-vector]
ok: [centos-7-vector]

TASK [Install Vector rpm package] **********************************************
skipping: [fedora-vector]
skipping: [ubuntu-vector]
ok: [centos-7-vector]

TASK [Download Vector rpm package] *********************************************
skipping: [centos-7-vector]
skipping: [ubuntu-vector]
ok: [fedora-vector]

TASK [Install Vector rpm package with dnf] *************************************
skipping: [centos-7-vector]
skipping: [ubuntu-vector]
ok: [fedora-vector]

TASK [Create vector config directory] ******************************************
ok: [centos-7-vector]
ok: [ubuntu-vector]
ok: [fedora-vector]

TASK [Create simple vector config for testing] *********************************
ok: [centos-7-vector]
ok: [fedora-vector]
ok: [ubuntu-vector]

TASK [Verify vector installation] **********************************************
ok: [ubuntu-vector]
ok: [fedora-vector]
ok: [centos-7-vector]

TASK [Display vector version] **************************************************
ok: [centos-7-vector] => {
    "msg": "Vector version: vector 0.21.0 (x86_64-unknown-linux-gnu c1edb89 2022-04-14)"
}
ok: [fedora-vector] => {
    "msg": "Vector version: vector 0.21.0 (x86_64-unknown-linux-gnu c1edb89 2022-04-14)"
}
ok: [ubuntu-vector] => {
    "msg": "Vector version: vector 0.21.0 (x86_64-unknown-linux-gnu c1edb89 2022-04-14)"
}

PLAY RECAP *********************************************************************
centos-7-vector            : ok=9    changed=0    unreachable=0    failed=0    skipped=4    rescued=0    ignored=0
fedora-vector              : ok=9    changed=0    unreachable=0    failed=0    skipped=4    rescued=0    ignored=0
ubuntu-vector              : ok=9    changed=0    unreachable=0    failed=0    skipped=4    rescued=0    ignored=0

INFO     default ➜ idempotence: Executed: Successful
INFO     default ➜ side_effect: Executing
WARNING  default ➜ side_effect: Executed: Missing playbook (Remove from test_sequence to suppress)
INFO     default ➜ verify: Executing

PLAY [Verify] ******************************************************************

TASK [Gathering Facts] *********************************************************
ok: [fedora-vector]
ok: [ubuntu-vector]
ok: [centos-7-vector]

TASK [Check if vector is installed] ********************************************
ok: [fedora-vector]
ok: [ubuntu-vector]
ok: [centos-7-vector]

TASK [Display vector version] **************************************************
ok: [centos-7-vector] => {
    "msg": "Vector version: vector 0.21.0 (x86_64-unknown-linux-gnu c1edb89 2022-04-14)"
}
ok: [fedora-vector] => {
    "msg": "Vector version: vector 0.21.0 (x86_64-unknown-linux-gnu c1edb89 2022-04-14)"
}
ok: [ubuntu-vector] => {
    "msg": "Vector version: vector 0.21.0 (x86_64-unknown-linux-gnu c1edb89 2022-04-14)"
}

TASK [Verify vector installation] **********************************************
ok: [centos-7-vector] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [fedora-vector] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [ubuntu-vector] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Check vector config file] ************************************************
ok: [centos-7-vector]
ok: [ubuntu-vector]
ok: [fedora-vector]

TASK [Verify config file exists] ***********************************************
ok: [centos-7-vector] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [fedora-vector] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [ubuntu-vector] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Validate vector config syntax] *******************************************
ok: [ubuntu-vector]
ok: [fedora-vector]
ok: [centos-7-vector]

TASK [Display validation result] ***********************************************
ok: [centos-7-vector] => {
    "msg": "Config validation: OK"
}
ok: [fedora-vector] => {
    "msg": "Config validation: OK"
}
ok: [ubuntu-vector] => {
    "msg": "Config validation: OK"
}

PLAY RECAP *********************************************************************
centos-7-vector            : ok=8    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
fedora-vector              : ok=8    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu-vector              : ok=8    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     default ➜ verify: Executed: Successful
INFO     default ➜ cleanup: Executing
WARNING  default ➜ cleanup: Executed: Missing playbook (Remove from test_sequence to suppress)
INFO     default ➜ destroy: Executing

PLAY [Destroy] *****************************************************************

TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item={'image': 'docker.io/pycontribs/ubuntu:latest', 'name': 'ubuntu-vector', 'pre_build_image': True})
changed: [localhost] => (item={'image': 'docker.io/pycontribs/centos:7', 'name': 'centos-7-vector', 'pre_build_image': True})
changed: [localhost] => (item={'image': 'docker.io/pycontribs/fedora:latest', 'name': 'fedora-vector', 'pre_build_image': True})

PLAY RECAP *********************************************************************
localhost                  : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     default ➜ destroy: Executed: Successful
INFO     default ➜ scenario: Pruning extra files from scenario ephemeral directory
WARNING  Molecule executed 1 scenario (1 missing files)

```
</details>  

### Tox

1. Добавьте в директорию с vector-role файлы из [директории](./example).
2. Запустите `docker run --privileged=True -v <path_to_repo>:/opt/vector-role -w /opt/vector-role -it aragast/netology:latest /bin/bash`, где path_to_repo — путь до корня репозитория с vector-role на вашей файловой системе.
3. Внутри контейнера выполните команду `tox`, посмотрите на вывод.
5. Создайте облегчённый сценарий для `molecule` с драйвером `molecule_podman`. Проверьте его на исполнимость.
6. Пропишите правильную команду в `tox.ini`, чтобы запускался облегчённый сценарий.
8. Запустите команду `tox`. Убедитесь, что всё отработало успешно.
9. Добавьте новый тег на коммит с рабочим сценарием в соответствии с семантическим версионированием.

После выполнения у вас должно получится два сценария molecule и один tox.ini файл в репозитории. Не забудьте указать в ответе теги решений Tox и Molecule заданий. В качестве решения пришлите ссылку на  ваш репозиторий и скриншоты этапов выполнения задания. 

### Решение Tox

---

### Как оформить решение задания

Выполненное домашнее задание пришлите в виде ссылки на .md-файл в вашем репозитории.