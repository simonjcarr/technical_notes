## Set VM Memory and Cores

```bash
config.vm.provider "virtualbox" do |v|
  v.memory = 4096
  v.cpus = 4
end
```
Ensure the above goes above the last `end` statement in the file

