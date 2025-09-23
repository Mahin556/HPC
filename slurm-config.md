• You have 3 RHEL VMs (for example: slurm-master, slurm-node1, slurm-node2).
• You have root (or sudo) access on all.
• Networking works (they can ping each other).
• You’ll use NFS to share files (recommended for production).

• On all nodes (master + workers):
```
sudo yum update -y
```
```
sudo yum groupinstall -y "Development Tools"
```
```
sudo yum install -y nfs-utils wget git curl \ 
			    gcc gcc-c++ make \ 
			    perl perl-ExtUtils-MakeMaker \ 
    			readline readline-devel \
    			rpm-build lua munge \
			    bzip2 zlib zlib-devel \
			    openssl openssl-devel \
    			http-parser json-c \
    			hwloc hwloc-devel \
    			lz4 lz4-devel hdf5 hdf5-devel \
			   	systemd-devel dbus-devel
```

• On all nodes:
```
Prepare /etc/hosts file
```
		
• On control node:
```
sudo echo "/cm/shared *(rw,sync,no_root_squash)" >> /etc/exports
sudo systemctl enable --now nfs-server
sudo exportfs -avf
```

• On worker nodes:
```
sudo mkdir -p /cm/shared
sudo mount slurm-master:/cm/shared /cm/shared
echo "slurm-master:/cm/shared /cm/shared nfs defaults 0 0" | sudo tee -a /etc/fstab
```			

• On control node:
```
cd /usr/local/src
wget https://github.com/dun/munge/releases/download/munge-0.5.16/munge-0.5.16.tar.xz
tar -xvf munge-0.5.16.tar.xz
cd munge-.5.16
./configure --prefix=/cm/shared/munge --sysconfdir=/etc --localstatedir=/var
make -j$(nproc)
sudo make install
```

• On all nodes:
```
sudo useradd --system --home /etc/munge --shell /sbin/nologin munge
sudo mkdir -p /etc/munge /var/lib/munge /var/log/munge /var/run/munge
sudo chown -R munge:munge /etc/munge /var/lib/munge /var/log/munge /var/run/munge
sudo chmod 0700 /etc/munge /var/lib/munge /var/log/munge /var/run/munge
```

• On control node:
```
sudo /cm/shared/munge/sbin/mungekey --create  ---> /etc/munge/munge.key
scp /etc/munge/munge.key root@slurm-node1:/etc/munge/
scp /etc/munge/munge.key root@slurm-node2:/etc/munge/
```

• On all nodes:
```
sudo chown munge:munge /etc/munge/munge.key
sudo chmod 400 /etc/munge/munge.key
setenforce 0
sudo sed -i 's/^SELINUX=.*/SELINUX=disabled/g' /etc/selinux/config
```

• On all nodes:
```
	[Unit]
	Description=MUNGE authentication service
	Documentation=man:munged(8)
	After=network.target
	
	[Service]
	Type=simple
	ExecStart=/cm/shared/munge/sbin/munged --foreground \
	    --key-file=/etc/munge/munge.key
	ExecReload=/bin/kill -HUP $MAINPID
	PIDFile=/var/run/munge/munged.pid
	User=munge
	Group=munge
	Restart=on-failure
	
	[Install]
	WantedBy=multi-user.target
```
```
sudo systemctl daemon-reload
sudo systemctl enable --now munge
systemctl status munge
```

• On all nodes:
```
/cm/shared/munge/bin/munge -n | /cm/shared/munge/bin/unmunge
export PATH=/cm/shared/munge/bin:$PATH
echo 'export PATH=/cm/shared/munge/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
munge -n | unmunge
```
			
• On all nodes:
```
sudo useradd -m slurm
sudo passwd slurm
sudo mkdir -p /cm/shared/slurm
sudo chown -R slurm:slurm /cm/shared/slurm
```

• On control node:
```
cd /usr/local/src
wget https://download.schedmd.com/slurm/slurm-24.05.3.tar.bz2
tar -xvjf slurm-24.05.3.tar.bz2
cd slurm-24.05.3
./configure --prefix=/cm/shared/slurm    --with-munge=/cm/shared/munge    --sysconfdir=/cm/shared/slurm    --with-hwloc    --enable-pam    --disable-slurmrestd    --with-hdf5=no
make -j$(nproc)
make install
```

• On control node:
```
vim /cm/shared/slurm/slurm.conf
```
```
	ClusterName=slurm_cluster
	ControlMachine=slurm-master
	SlurmUser=slurm
	SlurmdUser=root
	
	StateSaveLocation=/cm/shared/slurm/state
	SlurmdSpoolDir=/var/spool/slurmd
	
	AuthType=auth/munge
	SelectType=select/cons_tres
	SelectTypeParameters=CR_Core
	
	SchedulerType=sched/backfill
	SlurmctldPort=6817
	SlurmdPort=6818
	
	# Use cgroup v2 plugins
	ProctrackType=proctrack/cgroup
	TaskPlugin=task/cgroup
	JobAcctGatherType=jobacct_gather/cgroup
	
	NodeName=slurm-node[1-2] CPUs=1 RealMemory=1000 State=UNKNOWN
	PartitionName=debug Nodes=slurm-node[1-2] Default=YES MaxTime=INFINITE State=UP
```
```
vim /cm/shared/slurm/cgroup.conf
```
```
	ConstrainCores=yes
	ConstrainDevices=yes
	ConstrainRAMSpace=yes
	MaxRAMPercent=98
	AllowedSwapSpace=0
	AllowedRAMSpace=100
	MemorySwappiness=0
```
	
• On all nodes:
```
vim /etc/systemd/system/slurmd.service
```
```
	[Unit]
	Description=Slurm node daemon
	After=network.target munge.service
	
	[Service]
	ExecStart=/cm/shared/slurm/sbin/slurmd -D
	ExecReload=/bin/kill -HUP $MAINPID
	PIDFile=/var/run/slurmd.pid
	Restart=always
	User=root
	
	[Install]
	WantedBy=multi-user.target
```	
```
systemctl daemon-reload
systemctl enable --now slurmd.service
systemctl status slurmd.service
```

• On all nodes:
```
ls /cm/shared/slurm/lib/slurm | grep cgroup
---> output
```	
	
• Check:
```
sinfo
scontrol show nodes
```
