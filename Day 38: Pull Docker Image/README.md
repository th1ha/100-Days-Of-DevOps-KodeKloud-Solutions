# Pull Docker Image

* Pull `busybox:musl` image on App Server 3 in Stratos DC and re-tag (create new tag) this image as `busybox:blog`
  ```bash
  docker pull busybox:musl

  docker tag busybox:musl busybox:blog

  docker images
  ```