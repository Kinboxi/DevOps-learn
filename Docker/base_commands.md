# Docker — базові команди

## ps — список контейнерів
```bash
docker ps                  # працюючі контейнери
docker ps -a               # всі (в т.ч. зупинені)
docker ps -q               # тільки ID
docker ps -s               # з розміром диску
docker ps --filter status=exited
docker ps --latest         # останній створений
```

## run — запуск контейнера
```bash
docker run <image>                       # разовий запуск
docker run -d <image>                    # у фоні (detached)
docker run -it <image> sh                # інтерактив + shell
docker run --name mynginx -p 8080:80 nginx   # ім'я + порт (host:container)
docker run -v /host/path:/container/path <image>   # volume
docker run -e VAR=value <image>          # змінна середовища
docker run --rm <image>                  # видалити після зупинки
docker run -d --restart unless-stopped <image>     # автоперезапуск
```

## logs — логи
```bash
docker logs <container>                  # всі логи
docker logs -f <container>               # слідкувати в реальному часі (follow)
docker logs --tail 100 <container>       # останні 100 рядків
docker logs -t <container>               # з мітками часу
docker logs --since 30m <container>      # за останні 30 хв
docker logs --until 2026-10-07T12:00:00 <container>
```

## exec — виконання команд у працюючому контейнері
```bash
docker exec -it <container> sh           # зайти в shell
docker exec <container> cat /etc/hosts   # одна команда без входу
docker exec -u root <container> sh       # від імені root
docker exec -w /app <container> ls       # робочий каталог
```

## top / stats — процеси та ресурси
```bash
docker top <container>                   # процери всередині контейнера (ps)
docker stats                             # CPU/RAM/мережа всіх контейнерів
docker stats <container>                 # тільки одного
docker stats --no-stream                 # один знімок, без оновлення
```

## cp && diff
```bash
docker cp <container>:/path /host/path   # з контейнера на хост
docker cp /host/file <container>:/path   # з хоста в контейнер
docker diff <container>                  # зміни у файловій системі
# A - added, C - changed, D - deleted
```

## inspect — інформація
```bash
docker inspect <container>               # повний JSON (стани, мережа, mounts)
docker inspect --format '{{.State.StartedAt}}' <container>
docker inspect --format '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <container>
docker port <container>                  # мапінг портів
docker rename <old> <new>                # перейменувати
```

## життєвий цикл
```bash
docker start <container>                 # запустити зупинений
docker stop <container>                  # м'яка зупинка (SIGTERM, потім SIGKILL)
docker kill <container>                  # примусова зупинка (SIGKILL)
docker restart <container>               # перезапуск
docker pause <container> / docker unpause <container>   # заморозка (cgroup freeze)
docker wait <container>                  # чекати завершення, повертає exit-code
docker rm <container>                    # видалити контейнер
docker rm -f <container>                 # зупинити і видалити
```

## образи
```bash
docker pull <image>                      # завантажити
docker images                            # список (docker image ls)
docker images -a                         # з проміжними шарами
docker rmi <image>                       # видалити образ
docker image prune                       # висячі образи (-a — всі незадіяні)
docker system df                         # скільки місця займає Docker
```

## резервні копії
```bash
docker save -o backup.tar <image>        # образ у файл
docker load -i backup.tar                # образ з файлу
docker export -o cont.tar <container>    # файлова система контейнера
docker import cont.tar myimage:1.0       # створити образ із експорту
```
