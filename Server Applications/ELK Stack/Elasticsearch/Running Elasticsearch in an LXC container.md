I found I was getting OOM errors (Out of Memory) running elasticsearch in LXC. The fix was the following

```bash
nano /etc/elasticsearch/jvm.options.d/heap.options
```

Then add the following lines

```
-Xms1g
-Xmx1g
```
