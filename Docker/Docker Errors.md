If you get the following error
```console
ERROR: failed to create cluster: could not find a log line that matches "Reached target .*Multi-User System.*|detected cgroup v1"
```
Edit /etc/init.d/docker

Add the following line towards the top of the file

```
ulimit -n 32000
```
