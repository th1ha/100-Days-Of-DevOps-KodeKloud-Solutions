# Create a Docker Image From Container

* Create an image games:devops on Application Server 1 from a container ubuntu_latest that is running on same server
  ```bash
  docker ps
  ```
    <details>
      <summary>docker ps - output</summary>

        CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS          PORTS     NAMES
        49deada33f17   ubuntu    "/bin/bash"   55 seconds ago   Up 53 seconds             ubuntu_latest
    </details>

  ```bash
  docker commit ubuntu_latest games:devops
  
  # sha256:d210e914556dd0481b6569065040864a2087e40ef4e37fc908be4efe2742c6ab

  docker images
  ```
    <details>
      <summary>docker images - output</summary>
      
        REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
        games        devops    d210e914556d   4 seconds ago   132MB
        ubuntu       latest    6d79abd4c962   5 days ago      78.1MB
    </details>