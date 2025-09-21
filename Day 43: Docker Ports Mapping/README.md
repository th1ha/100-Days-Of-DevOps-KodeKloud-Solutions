# Docker Ports Mapping

* Pull `nginx:alpine-perl` docker image on `Application Server 2`
    - Create a container named `games` using the image you pulled
    - Map host port `3001` to container port `80`. Please keep the container in running state

  ```bash
  docker pull nginx:alpine-perl

  docker run -d --name games -p 3001:80 nginx:alpine-perl

  docker ps
  ```
    <details>
      <summary>outputs</summary>

        # docker pull nginx:alpine-perl
        
        alpine-perl: Pulling from library/nginx
        9824c27679d3: Pull complete 
        6bc572a340ec: Pull complete 
        403e3f251637: Pull complete 
        9adfbae99cb7: Pull complete 
        7a8a46741e18: Pull complete 
        c9ebe2ff2d2c: Pull complete 
        a992fbc61ecc: Pull complete 
        cb1ff4086f82: Pull complete 
        36859d91676f: Pull complete 
        Digest: sha256:b04b512a3daf265299d237dcef02909b4f3fa998800030d1beef22e29d36ee97
        Status: Downloaded newer image for nginx:alpine-perl
        docker.io/library/nginx:alpine-perl

        # docker run -d --name games -p 3001:80 nginx:alpine-perl

        docker run -d --name games -p 3001:80 nginx:alpine-perl

        # docker ps

        CONTAINER ID   IMAGE               COMMAND                  CREATED         STATUS        PORTS                  NAMES
        602428f2c49e   nginx:alpine-perl   "/docker-entrypoint.…"   4 seconds ago   Up 1 second   0.0.0.0:3001->80/tcp   games

    </details>