# Custom Zabbix Templates

This repo includes custom Zabbix templates, scripts, configurations and deployment scripts. It's meant to contain everything in one place as a single source of truth, and to be easily deployed across our infrastructure. 
## Repo Tree

```text
.
├── scripts
│   └── check-fstab-mounts
├── agent
│   └── cubes_zabbix.conf
├── deploy
│   ├── deploy-agent-conf
│   ├── deploy-agent-scripts
│   └── deploy-all
└── templates
    └── cubes-custom-zabbix-template.yaml
```

## scripts

Scripts directory contains all the custom bash scripts executed by items and evaluated by triggers which need to exist on the *Zabbix agent*.

### Requirements:

- All of them need to be executable, preferably, their path is specified in PATH environment variable (easiest to just place it in /usr/local/bin/)
- They have *strict* permissions and ownership: ```-rwxr-x--- 1 root zabbix```
- They require user parameter to be set in agent configuration

## agent

Agent directory contains custom configuration files which need to exist on *Zabbix agent*. We simply just specify where the script is located, and we can just access it in Zabbix itself through this user parameter.

### Requirements:

- They must be placed in the ```/etc/zabbix/zabbix_agentd.d/``` directory
- They have *strict* permissions and ownership: ```-rwxr----- 1 root zabbix```

## deploy

Contains custom deployment scripts which help us install everything on *Zabbix agents*.

```deploy-agent-conf``` and ```deploy-agent-scripts``` are essentially the same, they just put different files in different directories with different permissions. Simplest breakdown of their sequence is this:

For every file in the directory do the following:

1. Check if file already exist and if it's exactly the same
2. If yes, do nothing
3. If not, copy them over with correct permissions
4. If anything was copied over, restart zabbix agent service

```deploy-all``` however is a bit different. It's meant to be run from one place which would like to install everything. It has a following sequence:

1. ssh into a machine
2. wget the tarball of the repo and extract it
3. make deploy scripts executable
4. execute them
5. clean up space by deleting the repo

It's currently extremely simple and needs major improvements, so I will update the documentation as I develop it further. 

## templates

Contains custom template that needs to be imported on the *Zabbix server*. I researched about the way it could be easily deployed across multiple servers, it's possible to do so through curl POST requests, but it can vary from version to version of Zabbix, so I didn't bother thinking about it further. Since we don't have that many, it's not too big of a hassle to import templates manually, but we should think about making it easier in the future. 

After importing, it's necessary to add this custom template to desired hosts to start monitoring for custom metrics. So the template essentially contains items and triggers in one place. 
