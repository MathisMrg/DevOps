# DEVOPS 4IRC Mathis MALEK REPO

## Disposition des dossiers

- TP1 : TP et TD sur Docker ( Le docker compose le plus à jour est celui présent dans le dossier docker_compose_using_publish utiliser la variable .env.developement si on veut utiliser le docker compose)
- TP2 : TP sur GitHub Actions
- TP3 : TD et TP sur Ansible

## Tentative de load balancing (marche en local)
```
<Proxy "balancer://backencluster">
    BalancerMember "http://tp1-backend:8080"
    BalancerMember "http://tp1-backend2:8080"
</Proxy>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass /api/ balancer://backencluster/
    ProxyPassReverse /api/ balancer://backencluster/
    ProxyPass / http://front:80/
    ProxyPassReverse / http://front:80/
</VirtualHost>

LoadModule proxy_module modules/mod_proxy.so
LoadModule proxy_http_module modules/mod_proxy_http.so

LoadModule proxy_balancer_module modules/mod_proxy_balancer.so
LoadModule slotmem_shm_module modules/mod_slotmem_shm.so
LoadModule lbmethod_byrequests_module modules/mod_lbmethod_byrequests.so
```