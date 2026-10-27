# LogiTrack · Pipelines

Workflow **reutilizable** de CI/CD que invocan los 8 microservicios de LogiTrack. Cambiar el proceso aquí lo
cambia en todos los repositorios a la vez.

```
test ──needs──▶ docker ──needs──▶ deploy
lint, pytest,    imagen en GHCR      deploy hook de Render
pip-audit,       (SHA del commit     y verificación de /ready
gitleaks          + latest)          (entorno production)
```

- **test** se ejecuta en cada push y pull request. Si una prueba falla, `docker` y `deploy` no se ejecutan.
- **docker** y **deploy** solo se ejecutan en `main`: un pull request nunca despliega.
- Credenciales: la imagen se publica con el `GITHUB_TOKEN` temporal del propio workflow y el despliegue usa el
  secret `RENDER_DEPLOY_HOOK` de cada repositorio. Ninguna credencial está escrita en el YAML.

## Uso desde un microservicio

`.github/workflows/deploy.yml`:

```yaml
name: CI-CD fleet
on:
  push: { branches: [main] }
  pull_request: { branches: [main] }
  workflow_dispatch:
permissions:
  contents: read
  packages: write
jobs:
  pipeline:
    uses: Kevinso4/logitrack-pipelines/.github/workflows/ci.yml@main
    with:
      service-name: fleet
      imagen: ghcr.io/kevinso4/logitrack-fleet
      url: https://tracklogi-fleet.onrender.com
    secrets: inherit
```

## Microservicios que lo usan

| Microservicio | Repositorio | Estado |
|---|---|---|
| API Gateway | [logitrack-gateway](https://github.com/Kevinso4/logitrack-gateway) | [![CI-CD](https://github.com/Kevinso4/logitrack-gateway/actions/workflows/deploy.yml/badge.svg)](https://github.com/Kevinso4/logitrack-gateway/actions/workflows/deploy.yml) |
| Fleet | [logitrack-fleet](https://github.com/Kevinso4/logitrack-fleet) | [![CI-CD](https://github.com/Kevinso4/logitrack-fleet/actions/workflows/deploy.yml/badge.svg)](https://github.com/Kevinso4/logitrack-fleet/actions/workflows/deploy.yml) |
| Tracking | [logitrack-tracking](https://github.com/Kevinso4/logitrack-tracking) | [![CI-CD](https://github.com/Kevinso4/logitrack-tracking/actions/workflows/deploy.yml/badge.svg)](https://github.com/Kevinso4/logitrack-tracking/actions/workflows/deploy.yml) |
| Shipment | [logitrack-shipment](https://github.com/Kevinso4/logitrack-shipment) | [![CI-CD](https://github.com/Kevinso4/logitrack-shipment/actions/workflows/deploy.yml/badge.svg)](https://github.com/Kevinso4/logitrack-shipment/actions/workflows/deploy.yml) |
| Maintenance | [logitrack-maintenance](https://github.com/Kevinso4/logitrack-maintenance) | [![CI-CD](https://github.com/Kevinso4/logitrack-maintenance/actions/workflows/deploy.yml/badge.svg)](https://github.com/Kevinso4/logitrack-maintenance/actions/workflows/deploy.yml) |
| Routing | [logitrack-routing](https://github.com/Kevinso4/logitrack-routing) | [![CI-CD](https://github.com/Kevinso4/logitrack-routing/actions/workflows/deploy.yml/badge.svg)](https://github.com/Kevinso4/logitrack-routing/actions/workflows/deploy.yml) |
| Notification | [logitrack-notification](https://github.com/Kevinso4/logitrack-notification) | [![CI-CD](https://github.com/Kevinso4/logitrack-notification/actions/workflows/deploy.yml/badge.svg)](https://github.com/Kevinso4/logitrack-notification/actions/workflows/deploy.yml) |
| Billing | [logitrack-billing](https://github.com/Kevinso4/logitrack-billing) | [![CI-CD](https://github.com/Kevinso4/logitrack-billing/actions/workflows/deploy.yml/badge.svg)](https://github.com/Kevinso4/logitrack-billing/actions/workflows/deploy.yml) |
