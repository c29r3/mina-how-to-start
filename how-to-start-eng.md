## Relevance of the instruction
**Testnet 4.1**

1. Installing docker and wget  
`sudo apt update && sudo apt install -y docker.io wget`

2. Add the current user to the docker group, so as not to write `sudo docker` every time  
**The command is applied after after reconnect via ssh**    
`sudo usermod -aG docker $USER`

3. `<LINK_FROM_MAIL>` - link like `https: //raw.githubusercontent.com /.../ peers.txt`, which you should receive by mail  
`wget -O ~/peers.txt <LINK_FROM_MAIL>`

4. Copy the keys folder to your home directory. Use `scp` for Linux \ Mac or` winscp` for Windows  

5. Set the correct rights to the folder and key files    
```
chmod 600 $HOME/keys/my-wallet \
&& chmod 700 $HOME/keys
```

6. Run docker container. `12345` in the command below should be replaced with the password for your wallet    
```
cd; docker run --name mina -d \
--restart always \
-p 8301-8305:8301-8305 \
-p 127.0.0.1:3085:3085 \
-v $(pwd)/keys:/root/keys:ro \
-v $(pwd)/coda-config:/root/.coda-config \
-v $(pwd)/peers.txt:/root/peers.txt \
--env CODA_PRIVKEY_PASS='12345' \
minaprotocol/mina-daemon-baked:4.1-turbo-pickles-mina78ff1c7-autoe7f1072 daemon \
-block-producer-key /root/keys/my-wallet \
-peer-list-file /root/peers.txt \
-insecure-rest-server \
-log-level Info
```

7. Make sure the docker container doesn't reload every few seconds. If after running the command below the docker container periodically disappears from the list, then something went wrong and it is worth checking the logs  
`docker stats`

8. Check logs (only if the container is rebooted)    
`docker logs -f mina --tail 100`

9. You are waiting for synchronization. If the `Block height` will be equal to the` Max observed block length` but not equal to 1, then the node is probably synchronized. You can compare block heights with the explorer node - https://minaexplorer.com/status    
`watch docker exec mina coda client status`

10. After successful synchronization, you can start performing other tasks.   
To run any command from the official [Mina documentation](https://minaprotocol.com/docs) in the case of docker, you need to add before each command    
`docker exec mina`  
На примере `coda client status`  
`docker exec mina coda client status`


## Useful commands when using Docker:    
`docker ps` - will show a list of running containers and their uptime   
`docker stop mina` - stop the current container    
`docker restart mina` - reboot container  
`docker start mina` - start previously stopped container    
`docker rm -f mina` - delete runned container   
`docker system prune -af` - will remove un-running containers and unused docker images   
`docker exec -it mina bash` - with this command, you can go to the command shell of the running container.
In order not to write `docker exec ...` every time and execute commands directly in the container
