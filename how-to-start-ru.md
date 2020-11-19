Инструкция актуальна для Testnet 4.1

1. Устанавливаем docker и wget  
`sudo apt update && sudo apt install -y docker.io wget`

2. Добавляем текущего пользователя в группу docker, чтобы не писать каждый раз `sudo docker`  
`sudo usermod -aG docker $USER`

3. `<LINK_FROM_MAIL>` - ссылка вида `https://raw.githubusercontent.com/.../peers.txt`, которую вы должны получить по почте
`wget -O ~/peers.txt <LINK_FROM_MAIL>`

4. Копируете папку keys в домашнюю директорию. Используйте `scp` в случае с Linux\Mac или `winscp` в случае с Windows

5. Устанавливаете корректные права на папку и файлы ключей  
```
chmod 600 $HOME/keys/my-wallet \
&& chmod 700 $HOME/keys
```

6. Запускаете через докер  
```
cd; docker run --name mina -d \
--restart always \
-p 8301-8305:8301-8305 \
-p 127.0.0.1:3085:3085 \
-v $(pwd)/keys:/root/keys:ro \
-v $(pwd)/coda-config:/root/.coda-config \
-v $(pwd)/peers.txt:/root/peers.txt \
--env CODA_PRIVKEY_PASS='12345' \
minaprotocol/mina-daemon-baked:4.1-turbo-pickles-mina757342b-auto811bf26 daemon \
-block-producer-key /root/keys/my-wallet \
-peer-list-file /root/peers.txt \
-insecure-rest-server \
-log-level Info
```

7. Убеждаетесь что докер контейнер не перезагружается раз в несколько секунд. Если после запуска команды ниже докер контейнер периодически пропадает из спика, то что-то пошло не так и стоит проверить логи  
`docker stats`

8. Проверить логи (только если контейнер перезагружается)  
`docker logs -f mina --tail 100`

9. Дожидаетесь синхронизации. Если `Block height` будет равен `Max observed block length` но при этом не равнен 1, то вероятно нода синхронизирована. Вы можете сравнить высоту блоков с нодой эксплорера - https://minaexplorer.com/status  
`watch docker exec mina coda client status`

10. После успешной синхронизации можно приступать к выполнению других заданий. 
Для запуска любой команды из официальной документации Mina в случае  с докером вам надо перед каждой командой добавлять  
`docker exec mina`
На примере `coda client status`
`docker exec mina coda client status`


## Полезные команды при использовании Docker:  
`docker ps` - покажет список запущенных контейнеров и их аптайм  
`docker stop mina` - остановить текущий контейнер  
`docker restart mina` - перезагрузить текущий конейнер  
`docker start mina` - запустить остановленный контейнер  
`docker rm -f mina` - удалит запущенный контейнер  
`docker system prune -af` - удалит незапущенные контейнеры и неиспользуемые образы докера  
`docker exec -it mina bash` - этой командой можно перейти в командую оболочку запущенного контейнера, чтобы каждый раз не писать `docker exec ...` и выполнять команды напрямую в контейнере
