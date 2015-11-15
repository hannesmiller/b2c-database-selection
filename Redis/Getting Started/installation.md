

Redis is easy to install and it will take only couple of minutes for you to install on your machine. This is because it comes with a simple shell that provide you with everything you need and it doesn't have any external dependencies that you need to consider before installation.
 
There are several options available for you to install Redis either on your local machine or on a server depending on your system configurations. I will show here how to install it using the most two commonly used options.

Since Redis doesn't officially support windows, I will show the installation steps on linux-based and Mac systems. However there is a [windows port option](https://github.com/MSOpenTech/redis) developed by Microsoft Open Technologies that can be used.


#### 1- Building from the source:
Using the below scrip you can simply download, extract and build redis:

```
$ wget http://download.redis.io/releases/redis-3.0.5.tar.gz
$ tar xzf redis-3.0.5.tar.gz
$ cd redis-3.0.5
$ make

```

Now compiled binaries are inside src folder, to run the server run the below:

```
$ cd src
$ ./redis-server

```

You will get  a warning that configuration file (redis.conf) couldn't be found which is ok for now since Redis will use the defaults. We will talk about Redis configuration on details on later [section](link to the configuration section).

#### 2- Using package managers:
Installing redis using package managers is even more convienient and preferable for many people. Below you can see how to install Redis using the popular package managers:

Debian/Ubuntu:

```


```
sudo yum install redis

```

```
port install redis

```

```
brew install redis