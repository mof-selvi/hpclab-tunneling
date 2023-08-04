# hpclab-tunneling
Tunneling for hpclabs

thanks to [localtunnel](https://github.com/localtunnel/localtunnel)


# install jupyter
```
conda install -c anaconda jupyter
```


# define your password
```
jupyter notebook password
```



# go to ".jupyter/jupyter_notebook_config.json"
# add "notebook_dir":"/cta/users/{yourusername}", replace {yourusername} with your hpc username
# final look will be something like:

```
{
  "NotebookApp": {
    "password": "argon2:$argon2id$v=19$m=10240,t=10,p=8$yasdfasdfasdf",
    "notebook_dir":"/cta/users/{yourusername}"
  }
}
```




# start and test your JLab
```
/cta/users/{yourusername}/.conda/envs/myenv/bin/jupyter lab --no-browser --port=8889
```
# if it works, Ctrl+C to exit it.






# install nvm
```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash
```
```
source ~/.bashrc
```
```
nvm install v18.17.0
```







# install localtunnel
```
npm install -g localtunnel
```





# crate a file named tunnel.sh
```
nano tunnel.sh
```

## put these into it:
```
#!/bin/bash
if ! pgrep -x node >/dev/null
then
    lt --subdomain {yourusername}-myhpc --port 8889 > tunneling.log 2>&1 & 
fi
```






# crate a file named start.sh
```
nano start.sh
```


## put these into it:
```
#!/bin/bash
/cta/users/{yourusername}/.conda/envs/{yourenvironment}/bin/jupyter lab --no-browser --port=8889 > jlab.log 2>&1 & 
watch n30 ./tunnel.sh > watching.log 2>&1 & 
```



## set chmods
```
chmod +x ./tunnel.sh
```
```
chmod +x ./start.sh
```





## to start:
```
./start.sh
```



# the response will be something like "your url is: https://{yourusername}-hpc.loca.lt", connect directly to it
# the website will ask you the wan ip of the server, type: "95.183.239.163"




# to stop:
```
ps -aux | grep jupyter
```
```
kill -9 {processid}
```


```
ps -aux | grep watch
```
```
kill -9 {processid}
```


```
ps -aux | grep lt
```
```
kill -9 {processid}
```


# or create a file named stop.sh
```
#!/bin/bash
kill -9 $(pgrep -f bin/lt)
kill -9 $(pgrep -x watch)
kill -9 $(pgrep -x jupyter-lab)
```


## and do:
```
chmod +x stop.sh
```



# Now to stop the tunneling:
```
./stop.sh
```

