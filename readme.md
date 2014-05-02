# Docker: Ubuntu, Nginx Proxy

This is the basic for an Nginx Proxy server. This is based on [phusion/baseimage-docker](https://github.com/phusion/baseimage-docker) base Ubuntu image, which takes care of system issues which Docker's base Ubuntu image does not take care of, such as watching processes, logrotate, ssh server, cron and syslog-ng.

You can build this yourself after cloning the project (assuming you have Docker installed).

```bash
cd /path/to/repo/docker-nginx-proxy
docker built -t nx-proxy . # Build a Docker image named "nx-proxy" from this location "."
# wait for it to build...

# Run the docker container
docker run -v /path/to/local/sites-enabled/:/etc/nginx/sites-enabled:rw -v /path/to/local/log/:/var/log/nginx/log -p 80:80 -d nginx /sbin/my_init --enable-insecure-key
```

This will bind local port 80 to the container's port 80. This means you should be able to go to "localhost" in your browser (or the IP address of your virtual machine oh which Docker is running) and see your web application files.

* `docker run` - starts a new docker container
* `-v /path/to/local/web/files:/var/www:rw` - Bind a local directory to a directory in the container for file sharing. `rw` makes it "read-write", so the container can write to the directory.
* `-p 80:80` - Binds the local port 80 to the container's port 80, so local web requests are handled by the docker.
* `-d webapp` - Use the image tagged "webapp"
* `/sbin/my_init` - Run the init scripts used to kick off long-running processes and other bootstrapping, as per [phusion/baseimage-docker](https://github.com/phusion/baseimage-docker)
* `--enable-insecure-key` - Enable a generated SSL key so you can SSH into the container, again as per [phusion/baseimage-docker](https://github.com/phusion/baseimage-docker). Generate your own SSH key for production use.

# Guide to SSH into container

Find out the ID of the container that you just ran:

    docker ps

Once you have the ID, look for its IP address with:

    docker inspect <ID> | grep IPAddress

Now SSH into the container as follows:

    curl -o insecure_key -fSL https://github.com/phusion/baseimage-docker/raw/master/image/insecure_key
    chmod 600 insecure_key
    ssh -i insecure_key root@<IP address>