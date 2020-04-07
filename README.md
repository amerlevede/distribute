# distribute

Distribute processes over SSH. 

## `distribute`

Each line on the `stdin` is added to a queue of commands that will be executed by the machines given in the command line arguments.
The commands are automatically executed with `nice -n 19`, `ionice`, and *in the same directory* (thus this assumes a shared file structure).
The script also ensures that the target machine has at least 5 GB of free memory and 4 free CPU cores. Otherwise, the machine is not used until it is freed.

Sample usage:
```
for i in 1 2 3 4 5; do echo "echo $i - \$(hostname)"; done | distribute ${machine1} ${machine1} ${machine2}
```

This will distribute the commands `echo 1 - $(hostname)`, `echo 2 - $(hostname` etc., running two processes on `machine1` and one on `machine2`. The output is printed to `stdout` of the `distribute` process.

## `distribute-query`

Communicate with a running `distribute` script to get the current status, pause processes, kill subprocesses, etc. Use 
```distribute-query help```
for list of available commands.
