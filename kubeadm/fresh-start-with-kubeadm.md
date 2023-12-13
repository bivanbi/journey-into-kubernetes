# Fresh Start with kubeadm
There are certain settings that are complicated to change or changing them is not supported by `kubeadm` after the
cluster is created. At the beginning of the journey, it might be easier to start over than to fix the cluster.

## Reset Cluster
```shell
kubeadm reset
```

˙kubeadm` does not clean up everything. It will advise, though. Read the command output carefully.
Here are some tips where to look for leftover files and directories:
* `/etc/cni/net.d` - CNI configuration
* iptables and ip6tables rules
* `/var/lib/calico`

### Additional Cleanup Steps
* *The reset process does not clean CNI configuration. To do so, you must remove /etc/cni/net.d*
* *The reset process does not reset or clean up iptables rules or IPVS tables.
  If you wish to reset iptables, you must do so manually by using the "iptables" command.*
