# Splunk Universal Forwarder Setup

<!--
## 1. Prerequisites and Known Issues

Before installing the Splunk Universal Forwarder on an Ubuntu virtual machine,
note a common graphics driver warning that appears during boot on VMware-based hypervisors.

**Observed Error:**



![Ubuntu vmwgfx boot error](images/setup/0_ubuntu_error.png)

This error relates to the VMware graphics driver (vmwgfx) running under an
unsupported hypervisor configuration. It does not affect Splunk functionality and can be safely
disregarded for this setup. The system will boot and operate normally. To suppress the error
permanently, switch the VM display adapter to a supported type in your hypervisor settings.

-->

## 1. Installing the Universal Forwarder

Download the Splunk Universal Forwarder .deb package from splunk.com and install it using dpkg.

**Command:**

```bash
sudo dpkg -i splunkforwarder-10.2.2-80b90d638de6-linux-amd64.deb
```



![Splunk Universal Forwarder installation via dpkg](images/setup/1_uf_install.png)

The installer verifies system prerequisites and unpacks the package into /opt/splunkforwarder/.



## 2. Initializing Splunk for the First Time

Start Splunk and accept the license agreement. On first run, Splunk will prompt you to create
an administrator account.

**Command:**

```bash
sudo /opt/splunkforwarder/bin/splunk start --accept-license
```

**Steps during initialization:**

- Enter a username for the administrator account 
- Set and confirm a password 
- Splunk creates a systemd unit file and starts the daemon under the `splunkfwd` user

![Splunk first-time initialization and admin account creation](images/setup/2_splunk_initialisation.png)



## 3. Configuring the Forward Server

Point the Universal Forwarder to the Splunk indexer using the add forward-server command.
Verify network connectivity to the indexer before or after this step.

**Command:**

```bash
sudo /opt/splunkforwarder/bin/splunk add forward-server 192.168.56.1:9997
```


**Verify connectivity:**

To ensure that a proper connection between the forwarder and server check the following

```bash
ping 192.168.56.1
```

![Adding forward-server and verifying network connectivity via ping](images/setup/3_uf_add.png)





## 4. Adding a Log Monitor and Restarting

Configure the forwarder to monitor the /var/log/ directory and restart Splunk to apply changes.

**Commands:**

```bash
sudo /opt/splunkforwarder/bin/splunk add monitor /var/log/
sudo /opt/splunkforwarder/bin/splunk restart
```

![Adding /var/log monitor and restarting Splunk](images/setup/4_splunk_restart.png)



## 5. Verifying the Forwarder Connection

Confirm that the forwarder is actively connected to the indexer.

**Command:**

```bash
sudo /opt/splunkforwarder/bin/splunk list forward-server
```


![Listing active forward-server connections](images/setup/5_verification.png)

The output confirms that 192.168.56.1:9997 is an active forward destination.



## 6. Confirming Data Ingestion in Splunk Enterprise

Log in to the Splunk Enterprise web interface and run a search to confirm events are received.

**Search Query:**

```
index=main
```

**Result:**

- 19,706 events indexed over the last 24 hours (4/26/26 1:30 PM to 4/27/26 2:16 PM)
- Host: `ubuntu`
- Sources include: /var/log/syslog and 31 other log files
- Source types identified: 28 distinct types (e.g., syslog)

![Splunk Enterprise dashboard showing ingested events from the Ubuntu forwarder](images/setup/6_splunk_dashboard.png)


# References

[1] [Splunk Universal Forwarder Download Page](https://www.splunk.com/en_us/download/universal-forwarder.html?locale=en_us)  

[2] [Splunk Enterprise Download Page](https://www.splunk.com/en_us/download/splunk-enterprise.html) 

