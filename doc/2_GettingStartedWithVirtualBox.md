# Getting started with VirtualBox #

```powershell
docker-machine create --driver virtualbox dev
```

Output:

```powershell
Running pre-create checks...
Creating machine...
(dev) Copying C:\Users\sbc\.docker\machine\cache\boot2docker.iso to C:\Users\sbc\.docker\machine\machines\dev\boot2docker.iso...
(dev) Creating VirtualBox VM...
(dev) Creating SSH key...
(dev) Starting the VM...
(dev) Check network to re-create if needed...
(dev) Windows might ask for the permission to configure a dhcp server. Sometimes, such confirmation window is minimized in the taskbar.
(dev) Waiting for an IP...
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with boot2docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: <path-to-your-installation-of docker-tools>\docker-machine.exe env dev
```

Lets inspect the newly created virtual machine.

```powershell
 docker-machine env --shell powershell dev
```

...returns an output like the following:

```powershell
$Env:DOCKER_TLS_VERIFY = "1"
$Env:DOCKER_HOST = "tcp://192.168.99.103:2376"
$Env:DOCKER_CERT_PATH = "C:\Users\sbc\.docker\machine\machines\dev"
$Env:DOCKER_MACHINE_NAME = "dev"
$Env:COMPOSE_CONVERT_WINDOWS_PATHS = "true"
# Run this command to configure your shell:
# & "<path-to-your-installation-of docker-tools>\docker-machine.exe" env --shell powershell dev | Invoke-Expression
```

Copy the last line from your console (excluding the hashtag, but including the ampersand) and execute it from the console you wish to contine this tutorial from.

The IP address part of $Env:DOCKER_HOST should be entered in C:\Windows\System32\drivers\etc\hosts for dev.mymovies.local.

> **_NOTE_** You can stop the virtual machine with the command *docker-macine stop dev* and restart it with *docker-macine start dev*.

Lets'try and see that docker works. Open a PowerShell and type the following command:

```powreshell
docker run --name mynginx1 -P -d nginx:latest
```

This produces an output similar to the following:

```powreshell
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
8ec398bc0356: Already exists
465560073b6f: Pull complete
f473f9fd0a8c: Pull complete
Digest: sha256:b2d89d0a210398b4d1120b3e3a7672c16a4ba09c2c4a0395f18b9f7999b768f2
Status: Downloaded newer image for nginx:latest
089a326cfde2b4261645551a00d5ad3b4dfa358d1971a1b6c837ac3470bbef0c
```

Now we can verify that the container is running:

```powershell
docker container ls
```

```powershell
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                     NAMES
089a326cfde2        nginx               "nginx -g 'daemon of…"   14 seconds ago      Up 12 seconds       0.0.0.0:32768->80/tcp     mynginx1
```

The PORTS part of the above output is important here. It states that Nginx is running on port 80 as you would expect, but it is mapped to <http://dev.mymovies.local:32768> - you should replace 32768 with your own findings.

![Nginx running](Images/NginxRunning.png)

Let's stop the Docker container and clean up.

```powershell
docker container stop mynginx1
docker container rm mynginx1
docker image rm nginx:latest
```

Continue building a sample .NET Core Web App ['.NET Core Web App'](3_DotNetCoreWebApp.md)
