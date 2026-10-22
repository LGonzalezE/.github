# .github

Archivos comunes de todos los repos de `LGonzalezE`. GitHub los usa en cualquier repo que no tenga los suyos propios.

| Archivo | Para qué |
|---|---|
| `PULL_REQUEST_TEMPLATE.md` | Cuerpo de PR con change de OpenSpec, versión, despliegue en DEV y lista "Qué validar en DEV" |
| `ISSUE_TEMPLATE/` | Formularios de funcionalidad, error y seguridad. Ponen labels y assignee y agregan el issue al [Project Yggdrasil](https://github.com/users/LGonzalezE/projects/1) |
| `SECURITY.md` | Cómo reportar vulnerabilidades de forma privada |
| `.github/workflows/maven.yml` | CI reutilizable para Maven: build y tests con `mvnw` o `mvn` |
| `.github/workflows/python.yml` | CI reutilizable para Python: `requirements.txt` y pytest |
| `.github/workflows/npm.yml` | CI reutilizable para npm: `npm ci`, lint, tests y build |

Un repo que tenga su propia carpeta `.github/ISSUE_TEMPLATE` o su propio `PULL_REQUEST_TEMPLATE.md` deja de usar los de aquí para ese archivo.

Este repo es público porque GitHub solo aplica los archivos comunes desde un repo `.github` público. No guardes aquí nada sensible.

## CI reutilizable

Cada repo llama a estos workflows desde su propio `.github/workflows/ci.yml`:

```yaml
name: CI
on:
  pull_request:
  push:
    branches: [main]
concurrency:
  group: ci-${{ github.ref }}
  cancel-in-progress: true
permissions:
  contents: read
  packages: read
jobs:
  backend:
    uses: LGonzalezE/.github/.github/workflows/maven.yml@main
    with:
      working-directory: backend
      java-version: '21'
  frontend:
    uses: LGonzalezE/.github/.github/workflows/npm.yml@main
    with:
      working-directory: frontend
```

Si los tests necesitan una base de datos real, pasa `postgres-db: <nombre>`: el job levanta Postgres 16 y exporta `SPRING_DATASOURCE_URL`, `_USERNAME` y `_PASSWORD`.

Si el backend depende de un paquete de otro repo (el codec), pasa `secrets: { packages-token: ${{ secrets.CODEC_PACKAGES_TOKEN }} }`.
