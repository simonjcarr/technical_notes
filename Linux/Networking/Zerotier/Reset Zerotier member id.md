```bash
systemctl stop zerotier-one
```

Delete the files `identity.public` and `identity.secret` from ZeroTier's working diirectory, on linux this normally `/var/lib/zerotier-one`

```bash
systemctl start zerotier-one
```
