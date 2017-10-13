# zabbix-nvidia-smi-integration
This repository has a Zabbix template for monitoring multiple Nvidia graphics cards in particular the:

* GPU Utilisation
* GPU Power Consumption
* GPU Memory (Used, Free, Total)
* GPU Temperature
* GPU Fan Speed

The Zabbix template supports one or more Nvidia graphics cards and creates graphs for each of them separately.
It's my first version and probably some improvements will follow.

The information on how to configure the Zabbix agent is below. The template should be added to the server and Nvidia-SMI should be installed on the node that is to be monitored.

The following parameters need adding to the configuration file for the agent /etc/zabbix/zabbix_agentd.conf:

UserParameter=gpu.discovery,nvidia-smi -L| awk 'BEGIN{printf "{\"data\":["};{printf c"{\"{#GPUNAME}\":\""$$0"\"}";c=","};{gsub(":","");printf c"{\"{#GPU}\":\""$2"\"}";c=","};END{print "]}"}'
UserParameter=gpu.temp[*],nvidia-smi --query-gpu=temperature.gpu --format=csv,noheader,nounits -i $1
UserParameter=gpu.memtotal[*],nvidia-smi --query-gpu=memory.total --format=csv,noheader,nounits -i $1
UserParameter=gpu.used[*],nvidia-smi --query-gpu=memory.used --format=csv,noheader,nounits -i $1
UserParameter=gpu.free[*],nvidia-smi --query-gpu=memory.free --format=csv,noheader,nounits -i $1
UserParameter=gpu.fanspeed[*],nvidia-smi --query-gpu=fan.speed --format=csv,noheader,nounits -i $1
UserParameter=gpu.utilisation[*],nvidia-smi --query-gpu=utilization.gpu --format=csv,noheader,nounits -i $1
UserParameter=gpu.power[*],nvidia-smi --query-gpu=power.draw --format=csv,noheader,nounits -i $1

The following code was developed from https://github.com/RichardKav/zabbix-nvidia-smi-integration which wasfrom https://gist.github.com/bhcopeland/b54d3c678a0cb6e87119 and further refined to avoid the need to directly parse output from nvidia smi with grep and cut.

Alongside the template monitorgraphics.sh is an alternative monitoring script that outputs nvidia-smi information to disk. This is not needed for the Zabbix template but it remains useful for monitoring NVidia GPUs.

Wojciech
