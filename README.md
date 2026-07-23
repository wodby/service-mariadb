# MariaDB service for Kubernetes on Wodby

Run MariaDB as a database for Kubernetes applications managed by Wodby.

This repository defines the Wodby service manifests and operational
configuration for MariaDB.

- [Browse Wodby services](https://wodby.com/services)
- [Wodby service documentation](https://wodby.com/docs/2.0/services/)
- [Service manifest reference](https://wodby.com/docs/2.0/services/template/)

## Wodby stacks using this service

- [Drupal application stack](https://github.com/wodby/stack-drupal)
- [Laravel application stack](https://github.com/wodby/stack-laravel)
- [MariaDB application stack](https://github.com/wodby/stack-mariadb)
- [Matomo application stack](https://github.com/wodby/stack-matomo)
- [PHP application stack](https://github.com/wodby/stack-php)
- [WordPress application stack](https://github.com/wodby/stack-wordpress)

## Service overview

| Property | Manifest configuration |
| --- | --- |
| Service name | `mariadb` |
| Type | Database |
| Versions | `11.8` by default; also available: `11.4`, `11.2` |
| Workloads | `main` (StatefulSet), primary |
| Containers | `mariadb` using `wodby/mariadb` |
| Endpoints | `mariadb`: TCP 3306 |
| Volumes | Data, 10 GB |
| Helm | chart `oci://registry-1.docker.io/wodby/mariadb`; version `0.2.1` |
| Configuration | 3 generated or fixed tokens |
| Operations | 3 actions, 1 import workflows, 1 backup workflows |

## Use this service

Use this service through one of the Wodby application stacks listed above, or
reference `mariadb` from a custom Wodby stack.

A service is a reusable component and does not deploy by itself. The stack
defines its links, settings, versions, resources, and relationship to the rest
of the application.

## Maintain a custom version

1. Fork this repository.
2. Edit the service manifest and referenced files.
3. Import the repository as a [Git-backed service](https://wodby.com/docs/2.0/services/create/#create-a-git-backed-service).
4. Reference the service from a stack manifest.

Keep service, workload, container, endpoint, link, volume, config, and
derivative names stable unless dependent stacks and app-level overrides are
updated at the same time.

Validate the manifests with:

```bash
wodby service validate-manifest service.yml --org <org-id>
```

See the [service manifest reference](https://wodby.com/docs/2.0/services/template/) and the [managed services index](https://github.com/wodby/services).
