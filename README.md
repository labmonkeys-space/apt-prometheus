# APT monitoring with Prometheus

This repository contains all the assets to monitor APT upgrades and security upgrades from a Linux system using Prometheus.
It also tells you if a system needs a reboot.

### Requirements

* Node Exporter is deployed. I have tested it with Node_Exporter 1.5.0.
* Text file collector is enabled.
* Packages `moreutils` and `update-notifier-common` for `sponge` and the `apt-check` command. 

### Usage

**Step 1:** Install `sponge` and `apt-check` dependency packages

```
sudo apt install moreutils update-notifier-common
```

**Step 2:** Deploy metric wrapper script to `/usr/share/apt-metrics.sh`

```
sudo wget https://raw.githubusercontent.com/labmonkeys-space/apt-prometheus/main/script/apt-metrics.sh -O /usr/share/apt-metrics.sh
sudo chmod +x /usr/share/apt-metrics.sh
```

**Step 3:** Install APT hook to `/etc/apt/apt.conf.d`

```
sudo wget https://raw.githubusercontent.com/labmonkeys-space/apt-prometheus/main/apt.conf.d/60prometheus -O /etc/apt/apt.conf.d/60prometheus
```

**Step 4:** Configure Node_Exporter to fetch content from a `textfile` directory

The APT hook writes the `apt.prom` file to `/var/lib/node_exporter/textfile_collector`.

Configure your Node_Exporter to pick up the text file from the directory. 
The text file collector is enabled with `--collector.textfile` and the lookup directory is configured in `--collector.textfile.directory`.
Here is my example from the Node_Exporter `ExecStart` arguments defined in the Systemd unit.

```
--collector.textfile --collector.textfile.directory=/var/lib/node_exporter/textfile_collector
```

## Credits

Credits to [Tom Henderson](https://tom-henderson.github.io/2020/12/04/apt-grafana-prometheus.html) who did the heavy lifting in the apt-metrics.sh script.
He also gives some hints how to annotate the Grafana dashboard.
I've skipped this part and was not so helpful in my environment.
