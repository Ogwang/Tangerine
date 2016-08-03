# Tangerine

![Tangerine](http://static1.squarespace.com/static/55c4e56fe4b0852b09fa2f29/t/55c4ec18e4b0f8bc41c735ae/1461960019800/?format=1500w)

[![Build Status](https://travis-ci.org/Tangerine-Community/Tangerine.svg)](https://travis-ci.org/Tangerine-Community/Tangerine)

[![Stories in Ready](https://badge.waffle.io/Tangerine-Community/Tangerine.png?label=ready&title=Ready)](https://waffle.io/Tangerine-Community/Tangerine)

[![Join the chat at https://gitter.im/Tangerine-Community/Tangerine](https://badges.gitter.im/Tangerine-Community/Tangerine.svg)](https://gitter.im/Tangerine-Community/Tangerine?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

This Tangerine repository is in Beta. Use at your own risk, not recommended for production until 1.0.0.

Digitize your EGMA and EGRA data collection with Tangerine. Create your Assessments online, conduct them offline with the Tangerine Android App. All results you collect can be exported as a spreadsheet. Tangerine has been used by over 30 organizations to conduct over 1 million assessments and surveys in over 50 countries and in 100 languages.

This is one of two main Tangerine software repositories, the other is docker-tangerine-tree. The docker-tangerine-tree repository will soon be included with this one.
You will want to use this repository if you are planning on running a Tangerine server for editing assessments and uploading data from tablets.  

The client directory contains the code for the client app used for data entry. It has its own build process and is not yet 
integrated with server. Develop with this client code and copy it to the client directory in docker-tangerine-tree, and 
then tag the docker-tangerine-tree repository.

## Installation

See [installation demonstration video](https://vimeo.com/171423878).

The easiest way to get started is to create an Ubuntu 14.04 Droplet on [Digital Ocean](https://digitalocean.com). 

We also have [instructions for AWS](documentation/INSTALLATION_AWS.md) which include the following steps.

SSH into it from a terminal, [install Docker](https://docs.docker.com/engine/installation/linux/ubuntulinux/), and then run the following commands. You'll need the URL of the most recent release. Find that on the releases page [here](https://github.com/Tangerine-Community/Tangerine-server/releases).
```
wget <latest release, choose tar.gz option>
tar xvf <the tarball>
cd <the now uncompressed tangerine folder>
cp config.defaults.sh config.sh
# Edit the config.sh file to match your desired settings. Try `nano config.sh`. 
# You must edit all of the variables in the "Required to change" section. 
./install.sh
```

Now visit your Tangerine-server installation at the IP address or hostname of your installation.

If your server restarts or the container stops, you can later run the `./start.sh` script in the Tangerine-server folder. To upgrade your server, run the `./upgrade.sh` script. Note that if you update environment variables in `config.sh`, they will not be propogated to the `settings` doc in each group so certain paths will break. See [issue #114](https://github.com/Tangerine-Community/Tangerine/issues/114) for the status of this. 

## Develop on Tangerine 
To develop on Mac or Windows, this project requires a working knowledge of `docker` and `docker-machine`. While you can issue Docker commands from Windows or Mac, Docker containers cannot run directly run on those platforms (yet) so it requires connecting to another machine running Linux using the `docker-machine` command. To learn Docker, check out the [self-paced training on docker.io](https://training.docker.com/self-paced-training). 

Now that your `docker` command is connected to Linux machine it's time to get the code, but before you download the code, you will want to "fork it". Go to https://github.com/Tangerine-Community/Tangerine-server and click on the "Fork" button. Choose to fork the repository into your own personal Github account. From your forked version of the code, copy the clone URL and issue the following commands.

```
git clone <your clone url>
cd Tangerine-server
```

Now it's time to build and run your code. This first build will take up to 30 minutes depending on your Internet connection, processor power, and memory of your host machine. Future builds will take between 5 and 15 seconds because of the "Docker cache" you will be building on this first run. 

```
cp config.defaults.sh
# Edit config.sh to your desired setting. If you are using Docker Machine, make sure to set the T_HOST_NAME to the IP address of your Virtual Machine.
./build-and-run.sh
```

Tip: To speed up your first run of `build-and-run.sh`, take advantage of Docker Cache by downloading an already built image and copy it to the `tangerine/tangerine:local` tag that the `build-and-run.sh` script will build. Ex. `docker pull tangerine/tangerine:master; docker tag tangerine/tangerine:master tangerine/tangerine:local`. Note that this only works with Docker 1.9 and earlier because in newer versions of Docker, `docker pull` [does not pull the Docker Cache](https://github.com/docker/docker/issues/20316)..  

Now that you've built and run an image tagged as `local`, view your server from a web browser to confirm it is working. When you visit the site in a web browser, you will see output to your terminal indicating there is activity on the server. 

You are now ready to start modifying code. We use the Edit-Build-Run workflow. That means everytime you edit code, you will build and run the code with the `build-and-run.sh` script. If you make an edit that breaks the build, you will see that in the output in your terminal. If all is well, you can stop following the logs with `ctrl-c` and it's now time to make a code change and see that reflected in your browser. For example, edit `./editor/app/_attachments/index.html` and change the text in the `<title>` tag to be something like `<title>Hello Tangerine</title>`. If you reload your your browser you'll notice that the title tag has not changed. That's because the code your browser is viewing is based on the built image we made before the code change. To see our change run `./build-and-run.sh` again. 

Now follow the logs again and check your browser. If all is well, you should now see the title of the web page as "Hello Tangerine".


### Testing code inside of a container 

This goes against the grain of how Docker ethos: you should not modify the code inside your docker instance. In this case, 
we're making a trade-off due to the difficulties of developing this docker container locally. 

Follow the instructions in the Developers section. Get into a running container to play around.

```
docker exec -it tangerine-server-container /bin/bash 
```

Go to the code for editor:

````
cd /tangerine-server/editor
npm start default

````

This npm command will run the default gulp command and watch for changes in your code. 

SSH into another console to your server , docker exec into the same instance, edit your code. 

Please note: you must create a new group when you wish to view your changes.

Be sure to commit your code ASAP. Once your container is gone; any uncommitted changes will also be gone.

## Configuring the client app

As mentioned above, you should develope client here and copy to docker-tangerine-tree. If you are running docker-tangerine-tree 
on your own server, you must configure the relevant urls in the Content-Security-Policy section of index.html:

````
    <meta http-equiv="Content-Security-Policy"
          content="default-src *;
          style-src 'self' 'unsafe-inline';
          script-src 'self' https://*.tangerinecentral.org 'unsafe-inline' 'unsafe-eval' ;
          img-src 'self' data:;
          connect-src 'self' https://*.tangerinecentral.org data: blob: filesystem:">
````


## Network services map of Tangerine

Tangerine consists of various network services running on their respective ports and some staticly mounted directories for things like single page apps. Nginx is in front and handles most of the mapping of routes to specific ports. The `robbert` codebase is an acception as it is given a wildcard route from Nginx to handle whatever routes it cares to handle. In the future, we would like to consolidate all route handling into robbert. But for now, the network map looks like this. 

```
browser -- nginx on port 80 -\
                             |
                              \_`/*` -- wildcard route that is the `./robbert` codebase, a node app running on port xxx
                             |              |
                             |              \_ `/robbert/*` -- Routes for admin functionalities like creating new groups                                                                      
                             |              |
                             |              \_ `/app/:groupId/*` is a static content route that points to `./editor/app/*` codebase
                             |              |
                             |              \_ `/client/*` is a static content route that points to `./client/app/*` codebase
                             |                                                          
                              \_`/tree` is the `./tree` codebase, a node app running on port xxx      
                             |
                              \_`/_csv` is the `./brockman` codebase, a ruby app on port 3141
                             |
                              \_ `/_cors_bulk_docs` is the `./decompressor` codebase, a node app running on port 2989
                             |
                              \_ `/db/*` is couchdb on port 5984
```

