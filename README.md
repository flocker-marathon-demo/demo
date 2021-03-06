## Unofficial Plugins Demo

Using Flocker and Weave with the Docker platform

## Watch the video

See https://plugins-demo-2015.github.io/

Then come back here to try it yourself with Vagrant!

## Vagrant Quickstart

You will also need Vagrant 1.7.2 and Virtualbox installed.

Then:

```bash
$ git clone git@github.com/plugins-demo-2015/demo
$ cd demo
$ make -C vagrantrunner
```

This will use pre-packed boxes that has everything installed - start 2 VMs and then install the Flocker and Weave plugins.

This box uses the [Docker experimental binary](https://github.com/docker/docker/tree/master/experimental) and patched versions of [Swarm](https://github.com/binocarlos/swarm/tree/publish_service) and [Compose](https://github.com/binocarlos/compose/tree/publish_service):

Once the boxes have started - you should create the weave network on each of the nodes and start the swarm master:

```bash
$ cd vagrantrunner
$ bash create_network.sh
$ bash launch_scope.sh
$ bash swarm_manage.sh
```

Then in another shell, SSH into the master:

```bash
$ cd vagrantrunner
$ vagrant ssh master
```

Then we check that swarm is working and that the plugins are running, then bring up the app using `docker-compose`:

```bash
master# export DOCKER_HOST=localhost:2378
master# docker info
master# cd /vagrant/app
master# docker-compose up -d
master# docker ps -a | grep redis
```

Then we start the HTTP load balancer that opens up the open to the outside world:

```bash
master# cd /vagrant
master# bash run_proxy.sh
```

Now we can load the app in a browser:

```
http://172.16.70.250/
```

Click around and add some Docker logos onto the screen.

Now we migrate the stateful database to another node.

```
master# docker-compose stop
master# docker-compose rm -f
master# vim docker-compose.yml # switch "runner" and "master" in the redis constraint
master# docker-compose up -d
master# docker ps -a | grep redis
```

Observe that the database is both *still accessible* (thanks to Weave) and *still has its data* (thanks to Flocker).
