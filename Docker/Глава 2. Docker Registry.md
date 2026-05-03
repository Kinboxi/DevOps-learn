# Глава 2. Docker — Registry

> [!info] Зачем это знать
> Откуда берутся образы и как их скачивать.

---

## Что такое Docker Registry

Registry — это **хранилище образов**.

Как GitHub — но не для кода, а для образов.

**Docker Hub** — самый популярный registry: `hub.docker.com`

Там лежат тысячи готовых образов:
- `ubuntu` — базовая OS
- `python` — Python
- `nginx` — веб-сервер
- `mysql` — база данных

---

## Команды

### Скачать образ
```bash
docker pull <image_name>

# Примеры
docker pull nginx
docker pull ubuntu
docker pull python:3.10
```

> [!tip] Теги
> `python:3.10` — скачать конкретную версию.
> `python` без тега — скачает последнюю версию (`latest`).

---

> [!summary] Итог
> Registry — хранилище образов. Docker Hub — главный публичный registry. `docker pull` — скачать образ себе.
> 