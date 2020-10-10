Ansible.Role.Docker.Install.Traefik
=========

Rola instalująca i konfigurująca traefik.

Język: [EN](README.md), [PL](README.PL.md)

Zależnosci
--------------
Wymagany pakiet do szyfrowania haseł użytkowników:
```
Install apache2-utils for ubuntu
Install httpd-tools for centos
```

Zmienne w roli
--------------
requires:
```
  docker:           # Potrzebna konfiguracja dockera
  configuration:    # Konfiguracja traefika
  domains:          # Konfiguracja domen
  certificates:     # Lista certyfikatów
  users:            # Lista wszystkich użytkowników zdeklarowanych w inventory - Preferowane miejsce group_vars/all
  users_in_docker:  # Lista użytkowników do zdeklarowanych na serwisach docker`owych
```

example `docker`
```
docker:
  docker_compose_dir: /Data/containers
  docker_volumens_dir: /Data/containers
  docker_certicates_dir:  /etc/ssl/certs
```
example `configuration`
```
  configuration:
      open_ports:
        - name: https 
          internal: 443
          external: 443
        - name: http
          internal: 80
          external: 80
          redirection_to: https
      userslist: true
```
example `certificates`
```
  certificates:
    - name: rachuna.pl
      crt: << value >>
      key: << value >>
```
example `domains`
```
  domains:
    traefik:
      - name: rachuna.pl
      certificate_name: rachuna.pl
```
example `users_in_docker`
```
  users_in_docker:
    - name: maciej-rachuna
      docker_services:
        - traefik
```
example `users`
```
 users:
    - name: Maciej Rachuna
      username: maciej-rachuna
      default_password: 'aaa' 
      state: present
```

Przykładowy Playbook
----------------

Przykładowy playbbok
```
---
- hosts: localhost
  remote_user: root
  roles:
    - role: Ansible.Role.Docker.Install.Traefik
      tags: [traefik, docker, install]
      vars:
        docker:  
          docker_compose_dir:     /opt/docker_compose
          docker_volumens_dir:    /opt/containers
          docker_certicates_dir:  /etc/ssl/certs
        # configuration:       <- tests/inventory
        # certificates:        <- tests/inventory
        # domain:              <- tests/inventory
        # users_in_docker:     <- tests/inventory
        users:  # Recommendation: Defining in group_varss/all
          - name: Administrator
            username: administrator
            default_password: "aaa"
            state: present
      when:
        - "'traefik' in system.docker_services"
```
Przykładowe inventory
```
localhost:
  vars:
    configuration:
        open_ports:
          - name: https 
            internal: 443
            external: 443
          - name: http
            internal: 80
            external: 80
            redirection_to: https
        userslist: true
    certificates:
        - name: rachuna.pl
          crt: << value >>
          key: << value >>
    domains:
      traefik:
        domain_name: traefik.rachuna.pl
        certificate_name: "rachuna.pl"
      users_in_docker: 
        - name: administrator
          docker_services:
            - traefik

```

Testowanie
------------

Testing on:
  - Ubuntu 16.04 LTS
  - Ubuntu 18.04 LTS
  - Ubuntu 20.04 LTS
  - Centos 7
  - Centos 8

Licencja
-------

BSD

Autor
------------------
 **Maciej Rachuna**
##### System Administrator & DevOps Engineer
rachuna.maciej@gmail.com
