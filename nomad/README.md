Nomad
1. Provision Computer Resources (3-5 Nomad Servers) (1-1000 Clients)
2. Install the Nomad Binary
3. Configure systemd 
4. Create Nomad Config Files
5. Choose a Clustering Option
6. Enable Gossip Encryption
7. Enable mTLS Encryption
8. Enable ACLs
9. Start Nomad
10. Bootstrap the Initial ACL Token

port used for used where
4646 HTTP APi API client calls to Agents
4647 RPC agent to agent
4648 TCP/UDP Serf(gossip) Server to Server
20000-32000 TCP/UDP Dynamic ports Inbound traffic to Clients

download https://www.nomadproject.io/downloads
unzip it
Set permissions on the binary - sudo chown root:root nomad
Move into path sudo mv nomad /usr/local/bin/
Confirm install nomad version

Install any task driver software
Docker, java, Qemu

make nomad service file at 
/etc/systemd/system/nomad.service

contents: 

[Unit]
Description=Nomad
Documentation=https://www.nomadproject.io/docs
Wants=network-online.target
After=network-online.target

[Service]
ExecReload=/bin/kill -HUP $MAINPID
ExecStart=/usr/local/bin/nomad agent -config /etc/nomad.d
KillMode=process
KillSignal=SIGINT
LimitNOFILE=infinity
LimitNPROC=infinity
Restart=on-failure
RestartSec=2
StartLimitBurst=3
StartLimitIntervalSec=10
TasksMax=infinity

[Install]
WantedBy=multi-user.target


create config dir
sudo mkdir --parents /etc/nomad.d

sudo chmod 700 /etc/nomad.d

sudo touch /etc/nomad.d/nomad.hcl

contents 
datacenter = "dc1"
data_dir = "/opt/nomad"

Only on Server config 
create config file at 
/etc/nomad.d/server.hcl

sudo touch /etc/nomad.d/server.hcl

server {
  enabled = true
  bootstrap_expect = 3
}

Client Configuration
sudo touch /etc/nomad.d/client.hcl

client {
  enabled = true
}

Clustering 
server {
	...
	server_join {
		retry_join = ["server ip"]
	}
}

client {
	...
	server_join {
		retry_join = ["server ip"]
	}
}

If using Consul, it just works

Enable ACLs
Add config to /etc/nomad.d/nomad.hcl

acl {
	enable = true
}

nomad acl bootstrap to get inital token

Hands on Lab 
https://katacode.com/hashicorp/courses/securing-nomad
https://learn.hashicorp.com/nomad

Start Nomad
sudo systemctl enable nomad
sudo systemctl start nomad
sudo systemctl status nomad