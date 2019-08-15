# Introduction

Caching-Service is a docker based generic cache, which uses NGINX as the underlying server. This can be used to reduce latency in the internal LAN and avoid repeatative usage of the external access link. This can also be used to mimic a mirror server by using an authoritative server.

# Usage

The Cache-Service can handle only HTTP requests. Thus only the HTTP requests must be directed to it, which can done using DNS and SNI Proxy (to handle HTTPS).

## Prerequisites

* Installing Docker
  - Setting up the repository
  
    ```shell
    $ sudo apt-get update;
    $ sudo apt-get install \
      apt-transport-https \
      ca-certificates \
      curl \
      gnupg-agent \
      software-properties-common;
    $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -;
    $ sudo add-apt-repository \
     "deb [arch=arm64] https://download.docker.com/linux/ubuntu \
     $(lsb_release -cs) \
     stable";
    ```
  - Installing Docker CE
    
    ```shell
    $ sudo apt-get update;
    $ sudo apt-get install docker-ce docker-ce-cli containerd.io;
    $ sudo systemctl status docker;
    ```
   - Adding username to Docker group
   
      ```shell
      $ sudo usermod -aG docker ${USER};
      $ su - ${USER};
      ```
      
 * Installing curl
    
    ```shell
    $ sudo apt-get install curl
    ```   
 
 ## Building and Running Generic_Cache
 
 * Clonning this repository
 
    ```shell
    $ git clone https://github.com/steps-to-reproduce/Caching-Service.git;
    ```
 * Building Generic_Cache Docker image
    
    ```shell
    $ cd Generic_cache;
    $ docker build -t testing/generic_cache .
    ```
 * Creating disk space for cache
 
    ```shell
    $ mkdir /home/cache;
    $ mkdir /home/cache/<container name>;
    $ mkdir /home/cache/<container name>/data;
    $ mkdir /home/cache/<container name>/logs;
    ```
 * Running the docker container as a daemon
  
    ```shell
    $ docker run -td \
      --restart unless-stopped \
      --name <container name> \
      -v /home/cache/<container name>/data:/data/cache \
      -v /home/cache/<container name>/logs:/data/logs \
      -p <Your IP>:80:80 \
      testing/generic_cache:latest;
    ```  
 * The Generic_cache also has the capability of allowing only requests for only perticular `HOST_NAMES` (for ex: `*.example.com`). By default it is set to allow every non-void hosts. To enable this,
   
   ```shell
   $ docker run -td \
      --restart unless-stopped \
      --name <container name> \
      -v /home/cache/<container name>/data:/data/cache \
      -v /home/cache/<container name>/logs:/data/logs \
      -e ALLOWED_HOSTS="~(<RegExp for your HOST_NAMES>)" \
      -p <Your IP>:80:80 \
      testing/generic_cache:latest;
    ```
 * The Generic_cache also can be set to use your local cache as the `UPSTREAM_RESOLVER`. Its by default `8.8.8.8`.
   
   ```shell
   $ docker run -td \
      --restart unless-stopped \
      --name <container name> \
      -v /home/cache/<container name>/data:/data/cache \
      -v /home/cache/<container name>/logs:/data/logs \
      -e UPSTREAM_DNS="<Your local cache IP>" \
      -p <Your IP>:80:80 \
      testing/generic_cache:latest;
    ```

## Monitoring

 * Opening an iteractive bash on the container.
  
    ```shell
    $ docker exec -it <container name> bash
    ```
    
 * Executing the ```scripts/display.sh``` script to show the live status of the cache with colour coded output.
 
    ```shell
    $ ./display.sh
    ```
   This shows dynamically cache MISS or HIT or BYPASS etc. with colour coded outputs of the logs corresponding to that particular request. Upon terminating 
    its execution (Ctrl + C), it displays the MISS ration, HIT ration etc.

 * Executing the ```scripts/stats.sh``` script to show the complete status of the server from its Day 1 of deployment. This may take a while.:)
 
    ```shell
    $ ./stats.sh
    ```
    This shows total number of requests made to the server, number of cache MISS or HIT or BYPASS etc. 

## Testing and Monitoring

* Creating a Docker container named ```test```. bound to ```127.0.0.1```
  
  ```shell
    $ docker run -td \
      --restart unless-stopped \
      --name test \
      -v /home/cache/test/data:/data/cache \
      -v /home/cache/test/logs:/data/logs \
      -p 127.0.0.1:80:80 \
      testing/generic_cache:latest;
    ```

 * Opening an iteractive bash on the container.
  
    ```shell
    $ docker exec -it test bash
    ```
    
 * Execting the ```scripts/display.sh``` script to show the live status of the cache with colour coded output.
 
    ```shell
    $ ./display.sh
    ```
 
 * Execute ```/Caching-Service/Scripts/testing.sh``` on a differnt terminal (in your system not on the docker container).
 
    ```shell
    $ cd Scripts;
    $ ./testing.sh;
    ```
* Requesting for custom domains
  
  ```shell
  $ curl http://<custom domain>/<URI> --resolve <custom domain>:80:127.0.0.1
  ```
  
  For example
  
  ```shell
  $ curl http://ncert.nic.in/ --resolve ncert.nic.in:80:127.0.0.1
  ```

Look for cache MISS or HIT or BYPASS etc. on the shell executing ```display.sh```. Upon terminating its execution (Ctrl + C), it displays the MISS ration, HIT ration etc.


    
     
