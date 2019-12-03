# About ncclient  

ncclient is a python library. It is a NETCONF client implementation in Python.    

Code https://github.com/ncclient/ncclient  
Doc https://ncclient.readthedocs.io/en/latest/  
PyPI (Python Package Index) https://pypi.python.org/pypi/ncclient  

# About NETCONF

NETCONF is a protocol defined in the [RFC 6241](https://tools.ietf.org/html/rfc6241)   

ncclient is a NETCONF client  
Junos has a NETCONF server  

The file [NETCONF_session.md](NETCONF_session.md) shows how to use NETCONF in an SSH connection.  

# About Jupyter notebook

Jupyter Notebook is an open-source web application that allows you to create and share documents that contains live code.

# About this repo 

This repo has several Jupyter notebook files (`.ipynb` files). These files have Junos automation content using ncclient  

This repo also shows how to use NETCONF inside an SSH connection (file [NETCONF_session.md](NETCONF_session.md))

# Instructions 

## Requirements 

Install Docker 

You also need a Junos device.    

## Start Jupyter

Pull the Docker image for Jupyter
```
$ docker pull jupyter/minimal-notebook
```
```
$ docker images jupyter/minimal-notebook
REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
jupyter/minimal-notebook   latest              5741bc0030bd        2 weeks ago         2.57GB
```
Instanciate a container 
```
$ docker run --rm --name ncclient -d -p 9081:8888 -v $PWD:/home/jovyan/ jupyter/minimal-notebook
```
```
$ docker ps | grep jupyter/minimal-notebook
c57eac4264a9        jupyter/minimal-notebook   "tini -g -- start-no…"   10 minutes ago      Up 10 minutes       0.0.0.0:9081->8888/tcp                            ncclient
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
- The token is provided with the command `$ docker exec ncclient jupyter notebook list`

Then you can use Jupyter `.ipynb` files. They have ncclient content to automate Junos.  Suggested order: 
- [capabilities.ipynb](capabilities.ipynb)     
- [retrieve_configuration.ipynb](retrieve_configuration.ipynb)
- [update_configuration.ipynb](update_configuration.ipynb)
- [discard_changes.ipynb](discard_changes.ipynb)
- [create_xml_content.ipynb](create_xml_content.ipynb)
- [commit_comment.ipynb](commit_comment.ipynb)
- [execute_commands_or_junos_rpc.ipynb](execute_commands_or_junos_rpc.ipynb)

## Stop Jupyter 

```
$ docker stop ncclient
ncclient
$ docker ps | grep ncclient
$ docker ps -a | grep ncclient
$ 
```
The changes on the `.ipynb` files are automatically saved locally.  
```
$ ls -l
```
