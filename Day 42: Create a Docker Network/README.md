# Create a Docker Network

[Doc::: docker-network](https://docs.docker.com/reference/cli/docker/network/create/)

* Create a docker network named as `beta` on App Server 1. 
    - Configure it to use `bridge` drivers. 
    - Set it to use subnet `192.168.0.0/24` and iprange `192.168.0.0/24`
  ```bash
  # check the network list
  docker network ls

  docker network create -d bridge --subnet=192.168.0.0/24 --ip-range=192.168.0.0/24 beta
  ```
    <details>
      <summary>outputs</summary>

        # docker network ls
        NETWORK ID     NAME      DRIVER    SCOPE
        dbeaf4b2067d   bridge    bridge    local
        c82d46f6c422   host      host      local
        594982cceab2   none      null      local

        # docker network create -d bridge --subnet=192.168.0.0/24 --ip-range=192.168.0.0/24 beta
        c7a4724c72d98d3afd243dad1bc4855fe500ada0fd3f0747de0ab45fd5855724
    </details>


* Verify

  ```bash
  docker network ls

  docker network inspect beta
  docker 
  ```
  
    <details>
      <summary>verify-outputs</summary>

        # docker network ls
        NETWORK ID     NAME      DRIVER    SCOPE
        c7a4724c72d9   beta      bridge    local
        dbeaf4b2067d   bridge    bridge    local
        c82d46f6c422   host      host      local
        594982cceab2   none      null      local

        # docker network inspect beta
        [
            {
                "Name": "beta",
                "Id": "c7a4724c72d98d3afd243dad1bc4855fe500ada0fd3f0747de0ab45fd5855724",
                "Created": "2025-09-19T04:50:28.373427107Z",
                "Scope": "local",
                "Driver": "bridge",
                "EnableIPv6": false,
                "IPAM": {
                    "Driver": "default",
                    "Options": {},
                    "Config": [
                        {
                            "Subnet": "192.168.0.0/24",
                            "IPRange": "192.168.0.0/24"
                        }
                    ]
                },
                "Internal": false,
                "Attachable": false,
                "Ingress": false,
                "ConfigFrom": {
                    "Network": ""
                },
                "ConfigOnly": false,
                "Containers": {},
                "Options": {},
                "Labels": {}
            }
        ]
    </details>