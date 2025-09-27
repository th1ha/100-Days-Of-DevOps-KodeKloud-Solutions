# Docker Python App

* Create a `Dockerfile` under `/python_app` directory
  - Use any python image as the base image.
  - Install the dependencies using `requirements.txt` file.
  - Expose the port `5000`
  - Run the `server.py` script using `CMD`
* Build an image named `nautilus/python-app` using this Dockerfile
* Once image is built, create a container named `pythonapp_nautilus`
  - Map port `5000` of the container to the host port `8094`
* Once deployed, you can test the app using `curl` command on `App Server 2`


  <details>
    <summary>src/server.py</summary>
    
    ```py
    from flask import Flask
    # the all-important app variable:
    app = Flask(__name__)
    @app.route("/")
    def hello():
        return "Welcome to xFusionCorp Industries!"
    if __name__ == "__main__":
        app.config['TEMPLATES_AUTO_RELOAD'] = True
        app.run(host='0.0.0.0', debug=True, port=5000)
    ```
  </details>

  <details>
    <summary>src/requirements.txt</summary>

    ```
    flask
    ```
  </details>

  <details>
    <summary>Dockerfile</summary> 
      
    ```
    FROM python:3.12-alpine

    WORKDIR /python_app

    COPY src/requirements.txt .

    RUN pip install --no-cache-dir -r requirements.txt

    COPY src/server.py .

    EXPOSE 5000

    CMD ["python", "server.py"]
    ```
  </details>

---
* build & run the image

  ```bash
  docker build -t nautilus/python-app .

  docker images

  docker run -d --name pythonapp_nautilus -p 8094:5000 nautilus/python-app

  docker ps

  curl localhost:8094
  ```

  <details>
    <summary>outputs</summary>

      # docker build -t nautilus/python-app .

      [+] Building 190.7s (10/10) FINISHED                                  docker:default
      => [internal] load build definition from Dockerfile                            0.0s
      => => transferring dockerfile: 221B                                            0.0s
      => [internal] load metadata for docker.io/library/python:3.12-alpine         124.4s
      => [internal] load .dockerignore                                               0.0s
      => => transferring context: 2B                                                 0.0s
      => [1/5] FROM docker.io/library/python:3.12-alpine@sha256:02a73ead8397e904ce  60.7s
      => => resolve docker.io/library/python:3.12-alpine@sha256:02a73ead8397e904cea  0.0s
      => => sha256:1e894ae96196faeba587c992027c9f7e1eb510292fa 447.74kB / 447.74kB  30.3s
      => => sha256:2be5d4a24bdd9c5d19d0c07f535515b4d3ea69c3d5f8f 13.68MB / 13.68MB  30.6s
      => => sha256:02a73ead8397e904cea6d17e18516f1df3590e05dc8823 10.30kB / 10.30kB  0.0s
      => => sha256:617c4ee3fb984aaaddba496207a4d5d4e545f34fe0daef35 1.74kB / 1.74kB  0.0s
      => => sha256:09db1f0786a07d109f40e7954fbfcad6a911fd8b7d80ca1b 5.33kB / 5.33kB  0.0s
      => => sha256:9824c27679d3b27c5e1cb00a73adb6f4f8d556994111c12 3.80MB / 3.80MB  30.2s
      => => extracting sha256:9824c27679d3b27c5e1cb00a73adb6f4f8d556994111c12db3c5d  0.2s
      => => sha256:bd0b3be187e328a03074be666e59a16741e120b30ccd1eb9e62 247B / 247B  60.3s
      => => extracting sha256:1e894ae96196faeba587c992027c9f7e1eb510292fae54c20a8b3  0.3s
      => => extracting sha256:2be5d4a24bdd9c5d19d0c07f535515b4d3ea69c3d5f8f882ede6a  1.1s
      => => extracting sha256:bd0b3be187e328a03074be666e59a16741e120b30ccd1eb9e624e  0.4s
      => [internal] load build context                                               0.0s
      => => transferring context: 366B                                               0.0s
      => [2/5] WORKDIR /python_app                                                   0.2s
      => [3/5] COPY requirements.txt .                                               0.2s
      => [4/5] RUN pip install --no-cache-dir -r requirements.txt                    4.1s
      => [5/5] COPY server.py .                                                      0.3s 
      => exporting to image                                                          0.7s 
      => => exporting layers                                                         0.7s 
      => => writing image sha256:0e4eb7a0b665fdf87699bc69cb96b53729784ce9f9757d9e1d  0.0s 
      => => naming to docker.io/nautilus/python-app                                  0.0s

      # docker images

      REPOSITORY            TAG       IMAGE ID       CREATED          SIZE
      nautilus/python-app   latest    0e4eb7a0b665   24 seconds ago   61.4MB

      # docker run -d --name pythonapp_nautilus -p 8094:5000 nautilus/python-app

      23006bb9765f87fc6d7dc02217c4204726d26b88d02c3a288313ac39babb9a00

      # docker ps

      CONTAINER ID   IMAGE                 COMMAND              CREATED         STATUS        PORTS                    NAMES
      23006bb9765f   nautilus/python-app   "python server.py"   2 seconds ago   Up 1 second   0.0.0.0:8094->5000/tcp   pythonapp_nautilus

      # curl localhost:8094

      Welcome to xFusionCorp Industries!
  </details>



