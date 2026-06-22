Контейнеризация приложения

Быстрый старт

```bash

cp .env.example .env

docker compose build

docker compose up -d

```

### Безопасность Dockerfile


- **Многоэтапная сборка** — в финальный образ попадают только артефакты сборки, без исходников и инструментов разработки.

- **Непривилегированные пользователи** — `appuser` (UID 10001) в бэкенде, `nginx` во фронтенде.

- **Минимальные базовые образы** — `alpine` и `nginx-unprivileged`.

- **Статическая сборка Go** — `CGO_ENABLED=0`, флаги `-trimpath -ldflags="-s -w"`.

- **`.dockerignore`** — исключение тестов, `.git`, `node_modules` из контекста сборки.

- **Секреты не в образах** — переменные окружения передаются через `.env` / build-args при сборке, не хардкодятся в Dockerfile.

### Безопасность Docker Compose

  

- `security_opt: no-new-privileges:true` — запрет повышения привилегий.

- `read_only: true` + `tmpfs` — файловая система только для чтения, запись только во временные каталоги.

- `cap_drop: ALL` — сброс Linux capabilities.

- `backend-network: internal: true` — бэкенд изолирован от внешней сети.

- **Healthchecks** — контроль готовности сервисов.

- **Лимиты ресурсов** — ограничение CPU и памяти.

Секреты для Docker Hub (`DOCKER_USER`, `DOCKER_PASSWORD`) хранятся в GitHub Secrets, не в репозитории.
