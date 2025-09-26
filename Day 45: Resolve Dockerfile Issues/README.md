# Resolve Dockerfile Issues

* The `Dockerfile` is placed on `App Server 3` under`/opt/docker` directory
  - Fix the issues with this file and make sure it is able to build the image
  - Do not change base image, any other valid configuration within Dockerfile, or any of the data been used — for example, index.html

    <details>
    <summary>Dockerfile</summary> 
        
      ```ini
        FROM httpd:2.4.43

        RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf

        RUN sed -i '/LoadModule\ ssl_module modules\/mod_ssl.so/s/^#//g' conf/httpd.conf

        RUN sed -i '/LoadModule\ socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' conf/httpd.conf

        RUN sed -i '/Include\ conf\/extra\/httpd-ssl.conf/s/^#//g' conf/httpd.conf

        COPY /server.crt /usr/local/apache2/conf/server.crt

        COPY /server.key /usr/local/apache2/conf/server.key

        COPY ./index.html /usr/local/apache2/htdocs/
      ```
    </details>

    <details>
    <summary>ls -l </summary>

        # ls 

        Dockerfile  certs  html

        # ls certs/
        
        server.crt  server.key

        # ls html/
        
        index.html
        
    </details>
---
* fix the issues

  <details>
    <summary>fixed - Dockerfile</summary>

    ```ini
      FROM httpd:2.4.43

      RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf

      RUN sed -i '/LoadModule\ ssl_module modules\/mod_ssl.so/s/^#//g' /usr/local/apache2/conf/httpd.conf

      RUN sed -i '/LoadModule\ socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' /usr/local/apache2/conf/httpd.conf

      RUN sed -i '/Include\ conf\/extra\/httpd-ssl.conf/s/^#//g' /usr/local/apache2/conf/httpd.conf

      COPY ./certs/server.crt /usr/local/apache2/conf/server.crt

      COPY ./certs/server.key /usr/local/apache2/conf/server.key

      COPY ./html/index.html /usr/local/apache2/htdocs/
    ```
  </details>

  ```bash
    docker build -t web:v1 .

    docker run -d -p 8080:8080 --name test-container web:v1

    curl localhost:8080
  ```
    <details>
    <summary>outputs</summary>

      # docker build -t web:v1 .

      [+] Building 199.1s (13/13) FINISHED                                        docker:default
      => [internal] load build definition from Dockerfile                                  0.0s
      => => transferring dockerfile: 620B                                                  0.0s
      => [internal] load metadata for docker.io/library/httpd:2.4.43                     124.4s
      => [internal] load .dockerignore                                                     0.0s
      => => transferring context: 2B                                                       0.0s
      => [1/8] FROM docker.io/library/httpd:2.4.43@sha256:cd88fee4eab37f0d8cd04b06ef9728  63.6s
      => => resolve docker.io/library/httpd:2.4.43@sha256:cd88fee4eab37f0d8cd04b06ef97285  0.0s
      => => sha256:53729354a74c9c146aa8726a8906e833755066ada1a478782f4dfb 1.37kB / 1.37kB  0.0s
      => => sha256:f1455599cc2e008a4555f14451e590f071371d371a3b87790651a3 7.35kB / 7.35kB  0.0s
      => => sha256:bf59529304463f62efa7179fa1a32718a611528cc4ce9f30c0d 27.09MB / 27.09MB  30.9s
      => => sha256:3d3fecf6569b94e406086a2b68a7c8930254490b45c0de4911f497ea9 146B / 146B  30.3s
      => => sha256:b5fc3125d9129e4cdd43f496195cc8f39d43e9bad171044ecb5 10.37MB / 10.37MB  30.3s
      => => sha256:cd88fee4eab37f0d8cd04b06ef97285ca981c27b4d685f0321e65c 1.86kB / 1.86kB  0.0s
      => => sha256:3c61041685c0f65e0b375bae6ae6bdeab9b6c20960dbef5e302 24.47MB / 24.47MB  60.7s
      => => sha256:34b7e9053f76ca3c9dc574c5034679769256a596008efbfbff1d1b154 298B / 298B  60.4s
      => => extracting sha256:bf59529304463f62efa7179fa1a32718a611528cc4ce9f30c0d1bbc6724  2.7s
      => => extracting sha256:3d3fecf6569b94e406086a2b68a7c8930254490b45c0de4911f497ea9cf  0.4s
      => => extracting sha256:b5fc3125d9129e4cdd43f496195cc8f39d43e9bad171044ecb5b8f82b2f  1.1s
      => => extracting sha256:3c61041685c0f65e0b375bae6ae6bdeab9b6c20960dbef5e30201db18a4  1.5s
      => => extracting sha256:34b7e9053f76ca3c9dc574c5034679769256a596008efbfbff1d1b15466  0.7s
      => [internal] load build context                                                     0.0s
      => => transferring context: 3.19kB                                                   0.0s
      => [2/8] RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf   1.5s
      => [3/8] RUN sed -i '/LoadModule\ ssl_module modules\/mod_ssl.so/s/^#//g' /usr/loca  1.3s
      => [4/8] RUN sed -i '/LoadModule\ socache_shmcb_module modules\/mod_socache_shmcb.s  1.3s
      => [5/8] RUN sed -i '/Include\ conf\/extra\/httpd-ssl.conf/s/^#//g' /usr/local/apac  1.5s
      => [6/8] COPY ./certs/server.crt /usr/local/apache2/conf/server.crt                  0.9s
      => [7/8] COPY ./certs/server.key /usr/local/apache2/conf/server.key                  1.1s
      => [8/8] COPY ./html/index.html /usr/local/apache2/htdocs/                           1.1s
      => exporting to image                                                                2.4s
      => => exporting layers                                                               2.4s
      => => writing image sha256:930da22f0db703986b7383a97c1aa402f81b8f402d5007ff3d2ceddd  0.0s
      => => naming to docker.io/library/web:v1                                             0.0s

      # docker run -d -p 8080:8080 --name test-container web:v1

      f719ec40a6464dc4916ef91f27fa4a1747a119bdc9afe5c8c21caa95cfa818c6

      # curl localhost:8080

      This Dockerfile works!

    </details>

