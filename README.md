# hpclab-tunneling
Tunneling for hpclabs

thanks to [localtunnel](https://github.com/localtunnel/localtunnel)



# Install Jupyter
```
conda install -c anaconda jupyter
```


###### define your password
```
jupyter notebook password
```



###### go to ".jupyter/jupyter_notebook_config.json"
###### add "notebook_dir":"/cta/users/{yourusername}"
###### replace {yourusername} with your hpc username
###### the final look of the json file will be something like:
```
{
  "NotebookApp": {
    "password": "argon2:$argon2id$v=19$m=10240,t=10,p=8$yasdfasdfasdf",
    "notebook_dir":"/cta/users/{yourusername}"
  }
}
```




###### start and test your JLab
```
/cta/users/{yourusername}/.conda/envs/myenv/bin/jupyter lab --no-browser --port=8889
```
###### if it works, Ctrl+C to exit it.





---



# Install NVM & Localtunnel

###### Install nvm
```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash
```
```
source ~/.bashrc
```
```
nvm install v18.17.0
```





###### install localtunnel
```
npm install -g localtunnel
```




---

# hpct_tunnel.sh


###### crate a file named hpct_tunnel.sh
```
nano hpct_tunnel.sh
```

###### put these into it:
```
#!/bin/bash
if ! pgrep -u $(whoami) -f bin/lt >/dev/null
then
    lt --subdomain {yourusername}-myhpc --port 8889 > hpct_tunneling.log 2>&1 & 
fi
```


###### set chmods
```
chmod +x ./hpct_tunnel.sh
```





---

# hpct_jlab.sh


###### crate a file named hpct_jlab.sh
```
nano hpct_jlab.sh
```

###### put these into it:
```
#!/bin/bash
if ! pgrep -u $(whoami) -x jupyter-lab >/dev/null
then
    /cta/users/{yourusername}/.conda/envs/{yourenvironment}/bin/jupyter lab --no-browser --port=8889 > hpct_jlab.log 2>&1 & 
fi
```


###### set chmods
```
chmod +x ./hpct_jlab.sh
```






---

# hpct_start.sh


###### crate a file named hpct_start.sh
```
nano hpct_start.sh
```


###### put these into it:
```
#!/bin/bash
watch -n 120 ./hpct_jlab.sh > hpct_watching_jlab.log 2>&1 & 
watch -n 120 ./hpct_tunnel.sh > hpct_watching_tunnel.log 2>&1 & 
```



###### set chmods
```
chmod +x ./hpct_start.sh
```



###### to start:
```
./hpct_start.sh
```



###### the response will be something like "your url is: https://{yourusername}-hpc.loca.lt", connect directly to it
###### the website will ask you the wan ip of the server, type: "95.183.239.163"



---

# hpct_stop.sh


###### to stop:
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


###### or create a file named hpct_stop.sh
```
#!/bin/bash
kill -9 $(pgrep -u $(whoami) -f bin/lt)
kill -9 $(pgrep -u $(whoami) -x watch)
kill -9 $(pgrep -u $(whoami) -x jupyter-lab)
```


###### and do:
```
chmod +x hpct_stop.sh
```



###### Now to stop the tunneling:
```
./hpct_stop.sh
```

