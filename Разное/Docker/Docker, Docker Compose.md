Команды, которые мне пригодились.

docker-compose -f docker-compose.develop.yml down - Останавливает и удаляет контейнеры, созданные этим файлом.

docker-compose -f docker-compose.develop.yml up --build - Пересобирает образы и запускает контейнеры.

docker-compose -f docker-compose.develop.yml up -d - Запуск контейнеров из этого файла

docker exec -it <id or name container> sh or bash - Попасть внутрь контейнера 

docker ps - посмотреть список запущенных контейнеров 

docker ps -a  - посмотреть список всех контейнеров 

docker logs <id, name> - посмотреть логи контейнера 

docker stop <id, name> = останавливает контейнер по id или name

docker stop $(docker ps -q) - остановить все контейнеры 

docker system prune - Удаляет все неиспользуемые контейнеры, образы (как висящие, так и неиспользуемые) и, при необходимости, тома.



