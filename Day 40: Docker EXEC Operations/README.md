# Docker EXEC Operations

* Install `apache2` in `kkloud` container using `apt` that is running on `App Server 3`
  ```bash
  docker ps

  docker exec -it kkloud  bash

  apt update

  apt install apache2 -y
  ```
    <details>
      <summary>output</summary>

        # docker ps
        CONTAINER ID   IMAGE          COMMAND       CREATED         STATUS         PORTS     NAMES
        534cd836b254   ubuntu:18.04   "/bin/bash"   7 minutes ago   Up 7 minutes             kkloud
    </details>


* Configure Apache to listen on port `3002` instead of default `http` port. Do not bind it to listen on specific IP or hostname only, i.e it should listen on localhost, 127.0.0.1, container ip, etc.

  ```bash
  cat /etc/apache2/ports.conf | grep -i "Listen"
  
  sed -i "s/Listen 80/Listen 3002/" /etc/apache2/ports.conf

  service apache2 status

  service apache2 start
  ```
    <details>
      <summary>output</summary>
      
        # cat /etc/apache2/ports.conf | grep -i "Listen"
        Listen 80
        Listen 443
        Listen 443

        # service apache2 status
        * apache2 is not running

        # service apache2 start
        * Starting Apache httpd web server apache2
    </details>