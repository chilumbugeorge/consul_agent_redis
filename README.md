# consul_agent_redis

**Introduction**
Consul agent package for Redis

**Install Consul on a client**

You can download the latest consul from [here](https://releases.hashicorp.com/consul/).

```
cd /user/local/bin
sudo wget https://releases.hashicorp.com/consul/1.0.6/consul_1.0.6_linux_amd64.zip
sudo unzip consul_1.0.6_linux_amd64.zip
sudo rm consul_1.0.6_linux_amd64.zip
```

**Add consul user**
```
sudo adduser consul
```

**Note**: for password when prompted, just use consul as password

**Download the Consul Redis Package**
```
cd /etc
git clone git@github.com:chilumbugeorge/consul_agent_redis.git
sudo mv consul_agent_redis consul.d
```
**Update some config in the consul package**
Update the /etc/consul.d/config.json file and change values for the following config variables appropriately: 
1. "datacenter" 
2. "encrypt"   
3. "start_join"

"start_join" IPs should be those of the consul servers (ideally 3 or more, but always odd numbers of servers). For "encrypt", you can get generate the value by running the following command in the terminal:
```
consul keygen
```
Update the /etc/consul.d/services.json, /etc/consul.d/scripts/master.json and /etc/consul.d/scripts/slave.json service files and change the values for the following config variables appropriately:
1. "name" 

For example, if your redis server name is redis-session-1, then an appropriate nalue for name would be "redis-session". 

**Create some useful directories not included in the package**
```
sudo mkdir /var/consul
sudo chown consul:consul -R /var/consul
```
For Ubuntu 14.04 add the following config in the startup config file /etc/init/consul.conf:
```
description "Consul server process"

start on (local-filesystems and net-device-up IFACE!=lo)
stop on runlevel [!12345]

respawn

setuid consul
setgid consul

exec consul agent -node-id bab02cca-71e9-416e-bc1c-6533c454cf83 -config-dir /etc/consul.d/
```
**NOTE**: For the node-id value, you can run the following in the terminal to generate the uuid value:
```
(uuidgen | awk '{print tolower($0)}')
```
Then to run consul, we do
```
sudo start consul
```
For Ubuntu 16.04 add the following config in the startup config file /etc/systemd/system/consul.service:

[Unit]
Description=Consul
Documentation=https://www.consul.io/

[Service]
ExecStart=/usr/local/bin/consul agent -config-dir=/etc/consul.d/ 
ExecReload=/bin/kill -HUP $MAINPID
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
```
Then to run consul we do:
```
sudo systemctl daemon-reload
sudo systemctl start consul.service
sudo systemctl enable consul.service
```
 
