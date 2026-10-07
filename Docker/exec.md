# Docker exec
# Запуск контейнера у фоні
docker run -d <image_name>

# Запуск контейнера в інтерактивному режимі (із заходимо в shell):
docker run -it <image_name> sh

# Підключитися до ВЖЕ ПРАЦЮЮЧОГО контейнера (дебаг/логи):
docker exec -it <container_id> sh

# Виконати одну команду в контейнері без входу:
docker exec <container_id> cat /var/log/nginx/error.log

# Зупинити та видалити контейнер:
docker stop <container_id>
docker rm <container_id>
