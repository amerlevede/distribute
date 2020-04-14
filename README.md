# distribute

Distribute processes over SSH. 

## `distribute`

Each line on the `stdin` is added to a queue of commands that will be executed by the machines given in the command line arguments.
The commands are automatically executed with `nice -n 19`, `ionice`, and *in the same directory* as the controlling `distribute` script (this assumes that the computers running `distribute` and the one being `ssh`'d to share the same directory structure).
The script also ensures that the target machine has at least 5 GB of free memory and 4 free CPU cores. Otherwise, the machine is not used (the script checks every <5min if it has been freed).

## Sample usage

Let `commands` be a file containing:
```
sleep 5 && echo 1
sleep 5 && echo 2
```
then this:
```
cat commands | distribute roth roth
```
will run the two commands in two parallel processes on `roth`. It will finish after 5 seconds (+ overhead caused by ssh connection and CPU/memory checks) instead of 10, and could output 1 and 2 in either order.

Echo `1` and `2` on `roth` and `einstein`, together with the hostname of the computer actually executing each echo (should be each computer once, unless connecting to one happens to be much faster or one does not have enough CPU/memory)
```
{ echo "echo 1 - \$(hostname)"; echo "echo 2 - \$(hostname)"; } | distribute roth einstein
```
or the same from 1 to 5:
```
for i in 1 2 3 4 5; do echo "echo $i - \$(hostname)"; done | distribute roth einstein
```

The output is printed to `stdout` of the `distribute` process (use `command | distribute machines > outputfile`).

## `distribute-query`

Communicate with a running `distribute` script to get the current status, pause processes, kill subprocesses, etc. Use 
```distribute-query help```
for list of available commands. 

# For ATP

`che2` (more precisely `/mnt/server/local/new/bin/che2`) is a useful script that asks all the computers on the cluster if they are free. 

## Passwordless login

`distribute` only works if `ssh` can login to the other computers without prompting the user, which means that all computers which are not trusted for `ssh` connection without asking for a password or other confirmation will be skipped. 

Check the [zanzibar wiki](http://dev.thep.lu.se/zanzibar/wiki/SSHNoPassword) for instructions on that.

## Machines

To get a list of all machines on the cluster you might want to save this script:
```
grep -P "^130" /nfs/thep/machines.txt | column -t | cut -f 3 -d' '
```

