### Processes
`lsof -i :<port>` what process is listening on the port \
`lsof -i -P -n | grep LISTEN` `| NAME`  see what processes are listening at what port \
`ps aux | grep -i <search-term>` search all active processes \
`pidof <process-name>` find process id \
`top` top processes \
`kill <pid>`  `-9`  immediately, `-15` gracefully \

`systemctl start <service>` \
`service <service> start`

`service`: `postgresql`, `mongodb` \
`systemctl`: `redis`

