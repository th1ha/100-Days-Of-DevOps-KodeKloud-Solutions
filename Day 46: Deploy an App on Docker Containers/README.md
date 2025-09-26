# Deploy an App on Docker Containers

* On `App Server 1` in `Stratos Datacenter` create a docker compose file `/opt/data/docker-compose.yml` (should be named exactly)
* The compose should deploy two services (web and DB), and each service should deploy a container as per details below
  * **`For web service`**:
    - Container name must be `php_web`
    - Use image `php` with any `apache` tag. Check [here](https://hub.docker.com/_/php?tab=tags/) for more details
    - Map `php_web` container's port `80` with host port `8082`
    - Map `php_web` container's `/var/www/html` volume with host volume `/var/www/html`
  * **`For DB service`**:
    - Container name must be `mysql_web`
    - Use image `mariadb` with any tag (preferably latest). Check [here](https://hub.docker.com/_/mariadb?tab=tags/) for more details
    - Map `mysql_web` container's port `3306` with host port `3306`
    - Map `mysql_web` container's `/var/lib/mysql` volume with host volume `/var/lib/mysql`
    - Set `MYSQL_DATABASE=database_web` and use any custom user ( except root ) with some complex password for DB connections

* After running docker-compose up you can access the app with `curl command curl <server-ip or hostname>:8082/`
  - For more details check [here](https://hub.docker.com/_/mariadb?tab=description/)


    <details>
      <summary>docker-compose.yml</summary> 
        
      ```yaml
      services:
        web:
          container_name: php_web
          image: php:apache
          ports:
            - "8082:80"
          volumes:
            - /var/www/html:/var/www/html
          depends_on:
            - db
        db:
          container_name: mysql_web
          image: mariadb:latest
          ports:
            - "3306:3306"
          volumes:
            - /var/lib/mysql:/var/lib/mysql
          environment:
            MYSQL_DATABASE: database_web
            MYSQL_USER: web_user
            MYSQL_PASSWORD: P@ssw0rd!
            MYSQL_ROOT_PASSWORD: R00tBackUpPwd!
      ```
    </details>

---
* run docker-compose and verify

  ```bash
  docker compose up -d

  docker ps

  curl localhost:8082
  ```

  <details>
    <summary>outputs</summary>

      # docker compose up -d

      [+] Running 25/25
      ✔ db Pulled                                                                         25.3s 
        ✔ 953cdd413371 Pull complete                                                       6.4s 
        ✔ 3bf2e947a240 Pull complete                                                       7.2s 
        ✔ 32cbc8a9ec6b Pull complete                                                       9.0s 
        ✔ 0c69925a3f99 Pull complete                                                       9.8s 
        ✔ eeb61de4991e Pull complete                                                      10.7s 
        ✔ 650ac881f9b7 Pull complete                                                      21.5s 
        ✔ 52364fc3563c Pull complete                                                      23.6s 
        ✔ 5a1051e81802 Pull complete                                                      24.9s 
      ✔ web Pulled                                                                        57.7s 
        ✔ ce1261c6d567 Pull complete                                                       6.4s 
        ✔ 859a358271f6 Pull complete                                                       7.1s 
        ✔ cab1ef8e5751 Pull complete                                                      23.5s 
        ✔ 1f28489355a0 Pull complete                                                      27.7s 
        ✔ 844df6cd552d Pull complete                                                      30.0s 
        ✔ 41867ba49031 Pull complete                                                      31.4s 
        ✔ a50b4b077515 Pull complete                                                      32.9s 
        ✔ d40bf6ae10a4 Pull complete                                                      34.4s 
        ✔ fe059e2fa94f Pull complete                                                      35.8s 
        ✔ 823be171721e Pull complete                                                      38.3s 
        ✔ a4071c497534 Pull complete                                                      40.8s 
        ✔ 31e373db16d7 Pull complete                                                      45.0s 
        ✔ 553474ceb695 Pull complete                                                      49.5s 
        ✔ 5ffec0f7432c Pull complete                                                      53.4s 
        ✔ 4f4fb700ef54 Pull complete                                                      57.4s 
      [+] Running 3/3
      ✔ Network data_default  Created                                                      0.1s 
      ✔ Container mysql_web   Started                                                     13.7s 
      ✔ Container php_web     Started                                                      9.4s

      # docker ps

      CONTAINER ID   IMAGE            COMMAND                  CREATED              STATUS              PORTS                    NAMES
      012db77a537c   php:apache       "docker-php-entrypoi…"   About a minute ago   Up About a minute   0.0.0.0:8082->80/tcp     php_web
      36b72c1bbc4c   mariadb:latest   "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:3306->3306/tcp   mysql_web

      # curl localhost:8082

        <html>
        <head>
            <title>Welcome to xFusionCorp Industries!</title>
        </head>

        <body>
            Welcome to xFusionCorp Industries!    </body>
  </details>



