### Alpine-SpiderEnv-Py2

The environment in Docker for spider to get static or dynamic web pages html and analytic it.

#### Support

* requests
* selenium + chrome
* selenium + firefox
* beautifulsoup
* lxml
* Scrapy

#### No Support

* selenium + phantomjs

#### Environment variables

* `SUPER_ADMIN_NAME` supervisor web login user name (default `user`)
* `SUPER_ADMIN_PWD` supervisor web login user password (default `123456`)
* `CONFIG_DIR` the user spider programs config dir name (default `config`)

#### How to use

##### Get image from DockerHub

```
$ docker pull leafney/alpine-spiderenv-py2
```

##### Build image

```
$ docker build -t="leafney/alpine-spiderenv-py2" .
```

##### Create a default container

```
$ docker run --name spiderenv -d -p 9001:9001 -p 61208:61208 leafney/alpine-spiderenv-py2
```

And then open browser with url `http://localhost:9001` for supervisor web page by default user name `user` and password `123456` and open with url `http://localhost:61208` for glances web page.

##### Create a container to run your spider programs

###### First

Create the container with host directory `/home/tiger/spiderfile` (which can be set by yourself) mount to container default directory `/app` .

Here to change the supervisor web page user login password to `tiger` .

```
$ docker run --name spiderenv -v /home/tiger/spiderfile:/app -d -p 9001:9001 -p 61208:61208 -e SUPER_ADMIN_PWD=tiger leafney/alpine-spiderenv-py2
```

###### Second

Get the three directory under `/app` folder in the git library sample program copy to the `/home/tiger/spiderfile` directory：

```
spiderfile/
-- config
    -- hello.conf
-- logs
-- spider
    -- hello
        -- hello.py
```

* `config` This directory is used to store the configuration files that the crawler program is called by supervisor. The default extension is `*.conf`，and the directory folder name can be customized by the environment variable `CONFIG_DIR`,but it should be consistent
* `logs` directory used to store the crawler program is generated by the supervisor log file, can be specified in the `config` directory of the corresponding configuration file reptiles
* `spider` this directory is used to store the crawler programs and dependent files

***

In the `spider` directory, there is a default `hello.py` sample crawler.

The `hello.conf` configuration file under `config` directory:

```
;
;hello spider program
;

[program:spider_hello]   		            ; program name
command=python hello.py     				; the program (relative uses PATH, can take args)
directory=/app/spider/hello 	            ; directory to cwd to before exec (def no cwd)
user=root   								; setuid to this UNIX account to run the program
autostart=true                           	; start at supervisord start (default: true)
autorestart=true                         	; whether/when to restart (default: unexpected.May be one of false, unexpected, or true)
startsecs=10  								; number of secs prog must stay running (def. 1)
startretries=3                              ; max # of serial start failures (default 3)
stdout_logfile=/app/logs/hello_out.log       ; stdout log path, NONE for none; default AUTO
stderr_logfile=/app/logs/hello_err.log       ; stderr log path, NONE for none; default AUTO
```

The directory path of the configuration file in `*.conf` is the absolute path set for the program directory `/app` in the container. It is recommended to use an absolute path such as `directory=/app/spider/hello`. You can also use relative paths such as `directory=spider/hello`.

In addition, in order to prevent the problem caused by the permission denied of the program executing, the default implementation of the user is set to `root`.

***

###### Third

After the configuration files and reptiles to add to the corresponding directory, restart the container, through the web interface to view the crawler running.

```
$ docker restart spiderenv
```

***

#### Installation Library

* python 2.7.x
* sqlite3
* requests
* selenium + firefox + chrome
* beautifulsoup4
* xmltodict
* lxml
* Scrapy
* apscheduler
* supervisor
* glances
