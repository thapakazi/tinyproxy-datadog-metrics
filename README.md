# tinyproxy-datadog-metrics

This repo contains a Datadog agent check to monitor [Tinyproxy](https://tinyproxy.github.io/). The check will publish one service check to Datadog, called `tinyproxy`. It also records a handful of custom metrics about what the Tinyproxy process is doing.


## Installation

1. Configure Tinyproxy to report stats (assuming Tinyproxy is already installed):
    1. Copy `stats.json` to `/usr/share/tinyproxy`.
    1. In `/etc/tinyproxy.conf`, update the `StatFile` directive to point to `/usr/share/tinyproxy/stats.json`. Also make a note of the `StatHost` directive, if it's been changed from the default of `tinyproxy.stats`.
1. Configure the Datadog Agent to collect stats (assuming the agent is already installed):
    1. Copy `tinyproxy.py` to `/etc/datadog-agent/checks.d/`.
    1. Copy `tinyproxy.yaml` to `/etc/datadog-agent/conf.d/`.
    1. If you want to monitor a Tinyproxy instance other than `localhost:8888`, change the instance settings in the YAML file. Likewise, if Tinyproxy's `StatHost` doesn't match the default, you should change that here.
    1. Restart the `datadog-agent` service.


## List of custom metrics published

* `tinyproxy.response_time`: _gauge_, response time of the stats request, in seconds.
* `tinyproxy.opens`: _gauge_, number of open connections.
* `tinyproxy.reqs`: _monotonic counter_, number of requests processed.
* `tinyproxy.badconns`: _monotonic counter_, number of bad connections.
* `tinyproxy.deniedconns`: _monotonic counter_, number of denied connections.
* `tinyproxy.refusedconns`: _monotonic counter_, number of refused connections.

All monotonic counters reset when the `tinyproxy` process restarts.


## Easy setup instructions
```bash

# install datadog with:

datadog_api_key=xxx
DD_AGENT_MAJOR_VERSION=7 DD_API_KEY=${datadog_api_key} DD_SITE="datadoghq.com" bash -c "$(curl -L https://s3.amazonaws.com/dd-agent/scripts/install_script.sh)"

git clone https://github.com/thapakazi/tinyproxy-datadog-metrics.git /tmp/dd
cp /tmp/dd/stats.json /usr/share/tinyproxy/stats.json
sed -i -r 's!(^StatFile)(.*)!\1 /usr/share/tinyproxy/stats.json!' /etc/tinyproxy/tinyproxy.conf
cp tinyproxy.py /etc/datadog-agent/checks.d/
cp tinyproxy.yaml  /etc/datadog-agent/conf.d/
```
