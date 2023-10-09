## Docker
```sudo -i``` use terminal as root
### Image
```docker build . -t <image tag>```

```docker run -p <host port>:<container port> -d <image tag>``` 

```run``` is a combination of ```create``` and ```start```

```docker images``` lists all images

### Container
```docker container ls``` or ```docker ps ```  

```docker start <container id> -i``` interactive mode

```docker kill <container id>```

```docker logs <container id>``` 

```docker exec -it <container id> /bin/bash``` enters container shell

```docker stop $(docker ps -a -q)``` stop all containers

```ctrl``` + ```d```exit container: ctrl+d

```sudo groupadd docker sudo usermod -aG docker $USER``` give regular user docker permissions

### Compose
```docker compose up --build -d``` rebuilds. detached.
