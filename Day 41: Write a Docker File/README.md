# Write a Docker File

* Use `ubuntu:24.04` as the base image.
  ```bash
  vi /opt/docker/Dockerfile
  ```
    <details>
      <summary>Dockerfile</summary>

        FROM ubuntu:24.04

        RUN apt update && apt install -y apache2 && apt clean

        RUN sed -i "s/Listen 80/Listen 6400/" /etc/apache2/ports.conf

        EXPOSE 6400

        CMD ["apache2ctl", "-D", "FOREGROUND"]
    </details>


* Testing

  ```bash
  docker build -t testing:v1 .
  
  docker run -d -p 6400:6400 testing:v1

  curl localhost:6400
  ```