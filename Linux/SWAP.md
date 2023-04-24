## Turn off Swap

### For this session only
```bash
swapoff -a
```

## Permanently disable swap
```bash
nano /etc/sysctl.conf
```

Then add the following line
```bash
vm.swappiness=10
```

