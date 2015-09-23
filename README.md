docker-nginx-busybox
====================

This is a teeny tiny docker image for nginx built on top of busybox.

Manually build a docker image
-----------------------------
On the docker host, perform the command:

    docker build --tag=nginx-busybox:latest .

Verify that the image has been created:

    docker images
    
should lead to an output similar to:

    REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
    nginx-busybox       latest              5c21ee35c67c        8 hours ago         7.898 MB

Start Container from the above created image
--------------------------------------------
On the docker host, perform the command:

    docker run -d -p 80:80 nginx-busybox

Verify that the container has been created and is up and running:

    docker ps
    CONTAINER ID        IMAGE                  COMMAND             CREATED             STATUS              PORTS                         NAMES
    a8614bead979        nginx-busybox:latest   "/usr/sbin/nginx"   4 seconds ago       Up 3 seconds        0.0.0.0:80->80/tcp, 443/tcp   stoic_mestorf

Connect to the NginX reverse Proxy locally
------------------------------------------
On the docker host, the commands

    export http_proxy=
    curl localhost
    
Should print the content of index.html to the screen:

    <!DOCTYPE html>
    <html>
    <head>
    <title>Welcome to nginx on busybox!</title>
    ...
    <p><em>Thank you for using nginx.</em></p>
    </body>
    </html>

Connect to the NginX reverse Proxy per Browser
----------------------------------------------
Linux:
If you start a browser on the Linux docker host, use the URL 

    http://localhost
    
Windows or iOS:
On boot2docker hosts (Windows and iOS), find your docker host's IP address by issuing the command 
    
    boot2docker ip
     
on the boot2docker start commandline window. Usually, this is a Virtualbox host-only network and is reachable from the Windows or iOS host only, but not from the outside world.

On Windows or iOS using a custom Linux Virtualbox VM as docker host (as we recommend since the installation of boot2docker is a nightmare sometimes), networking can be more tricky:

On the Linux docker host, issue the command

    ifconfig
    
The interface docker0 and eth0 with IP address 10.0.2.15 are not reachable from the Windows or iOS host. Therefore, we recommend to provision the custom Linux Virtualbox VM with an additional interface eth1 with a host-only (if it does not need to be reached from outside the Windows or iOS host) or bridged network. In this case the output of ifconfig will look something like  

    core@core-01 ~/docker-nginx-busybox $ ifconfig
    docker0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
            inet 10.1.42.1  netmask 255.255.0.0  broadcast 0.0.0.0              <--- not reachable from the outside!!
            ...
    
    eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
            inet 10.0.2.15  netmask 255.255.255.0  broadcast 10.0.2.255         <--- not reachable from the outside!!
            ...
    
    eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
            inet 172.17.8.101  netmask 255.255.255.0  broadcast 172.17.8.255    <--- host-only network -> only reachable from the Virtualbox host
            ...

From the Windows or iOS host, open an Internet browser and put an URL like

    http://<IP address of eth1>.17.8.101

to the URL field; in the example above, this is http://172.17.8.101.
