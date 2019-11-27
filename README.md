# About ncclient  

ncclient is a python library.  
It is a NetConf client implementation in Python.    
Code https://github.com/ncclient/ncclient  
Doc https://ncclient.readthedocs.io/en/latest/  
PyPI (Python Package Index) https://pypi.python.org/pypi/ncclient  

# About NETCONF

NETCONF is a protocol defined in the [RFC 6241](https://tools.ietf.org/html/rfc6241)   
Junos has a NETCONF server  
ncclient is a NETCONF client  

The file [NETCONF_session.md](NETCONF_session.md) shows how to open a NETCONF session in an SSH connection.  

# About Jupyter notebook

Jupyter Notebook is an open-source web application that allows you to create and share documents that contains live code.

# About this repo 

The notebook [ncclient.ipynb](ncclient.ipynb) file has a Junos automation demo using ncclient  

This repo also shows a NETCONF session inside an SSH connection (file [NETCONF_session.md](NETCONF_session.md))

# Instructions 

Install Docker 

Pull the Docker image for Jupyter
```
$ docker pull jupyter/minimal-notebook
```
```
$ docker images jupyter/minimal-notebook
REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
jupyter/minimal-notebook   latest              5741bc0030bd        2 weeks ago         2.57GB
$
```
Instanciate a container 
```
$ docker run --rm --name ncclient -d -p 9081:8888 -v $PWD/ncclient.ipynb:/home/jovyan/ncclient.ipynb jupyter/minimal-notebook
```
```
$ docker ps | grep jupyter/minimal-notebook
c57eac4264a9        jupyter/minimal-notebook   "tini -g -- start-no…"   10 minutes ago      Up 10 minutes       0.0.0.0:9081->8888/tcp                            ncclient
```
Get the token (it is required to log in to the Jupyter GUI 
```
$ docker exec ncclient jupyter notebook list
```
Install the requirements on the container 
```
$ docker exec ncclient pip install ncclient
```
```
$ docker exec ncclient pip list | grep ncclient
ncclient               0.6.6
```
Open a browser.  
The URL is `http://IP:PORT/?token=xxxxxxxx`   
- The default port is 8888 but the container instanciated with the above `docker run` command use the port 9081  
- The token is provided with the command `docker exec ncclient jupyter notebook list`

Then you can use the file [ncclient.ipynb](ncclient.ipynb)]. It has ncclient content to automate Junos. You also need a Junos device.    
