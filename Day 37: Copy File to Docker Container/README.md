# Day 37: Copy File to Docker Container

* Copy an encrypted file /tmp/nautilus.txt.gpg from the docker host to the ubuntu_latest container located at /usr/src/. Ensure the file is not modified during this operation.
  ```bash
  docker cp /tmp/nautilus.txt.gpg ubuntu_latest:/usr/src/

  # Successfully copied 2.05kB to ubuntu_latest:/usr/src/
  ```

* verify
  ```bash
  docker exec ubuntu_latest ls /usr/src

  # nautilus.txt.gpg
  ```