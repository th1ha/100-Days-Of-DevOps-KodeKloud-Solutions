# Write a Docker Compose File

1. Docker Compose
    - On `App Server 3` in `Stratos DC` create a container named `httpd` using a docker compose file `/opt/docker/docker-compose.yml` (please use the exact name for file)
    - Use `httpd` (preferably `latest` tag) image for container and make sure container is named as httpd; you can use any name for service
    - Map `80` number port of container with port `8089` of docker host
    - Map container's `/usr/local/apache2/htdocs` volume with `/opt/data` volume of docker host which is already there. (please do not modify any data within these locations)

    <details>
    <summary>docker-compose.yml</summary> 
        
        services:
        http_web:
            image: httpd:latest
            container_name: httpd
            ports:
            - "8089:80"
            volumes:
            - "/opt/data:/usr/local/apache2/htdocs"
    </details>
---
2. run docker compose

    ```bash
    docker compose up -d

    docker ps

    curl localhost:8089
    ```

      <details>
        <summary>docker-compose.yml</summary>

          # docker compose up -d

          [+] Running 7/7
          ✔ http_web Pulled                                                     6.1s 
            ✔ ce1261c6d567 Pull complete                                        2.7s 
            ✔ aeb6d226161f Pull complete                                        3.1s 
            ✔ 4f4fb700ef54 Pull complete                                        3.5s 
            ✔ 56926e6ce68f Pull complete                                        4.1s 
            ✔ 4938babf7b43 Pull complete                                        5.3s 
            ✔ 307fcc49c641 Pull complete                                        5.9s 
          [+] Running 2/2
          ✔ Network docker_default  Created                                     0.1s 
          ✔ Container httpd         Started                                     1.6s 

          # docker ps

          CONTAINER ID   IMAGE          COMMAND              CREATED              STATUS              PORTS                  NAMES
          25fdcdf68ecf   httpd:latest   "httpd-foreground"   About a minute ago   Up About a minute   0.0.0.0:8089->80/tcp   httpd

          # curl localhost:8089
          
          <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">
          <html>
          <head>
            <title>Index of /</title>
          </head>
          <body>
          <h1>Index of /</h1>
          <ul><li><a href="index1.html"> index1.html</a></li>
          </ul>
          </body></html>
      </details>