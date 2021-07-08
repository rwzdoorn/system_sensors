# RPI System sensors
[![GitHub Release][releases-shield]][releases]
[![License][license-shield]](LICENSE.md)

This is a fork of [Sennevds/system_sensors](https://github.com/Sennevds/system_sensors). Aside from a few qol changes, there is functionally no big difference between this fork and the original.

It currently logs the following data:
* CPU usage
* CPU temperature
* CPU Clock Speed
* Disk usage
* Memory usage
* Power status of the RPI
* Last boot
* Last message received timestamp
* Swap usage
* Wifi signal strength
* Wifi connected SSID
* Amount of upgrades pending
* Disk usage of external drives
* Hostname
* Host local IP
* Host OS distro and version
* CPU Load (1min, 5min and 15min)
* Network Download & Upload throughput

# System Requirements

You need to have at least __python 3.6__ installed to use System Sensors.

# Installation:
1. Clone this repo >> git clone https://github.com/SvbZ3r0/system_sensors.git
2. cd system_sensors
3. pip3 install -r requirements.txt
4. sudo apt-get install python3-apt
5. Edit settings_example.yaml in "~/system_sensors/src" to reflect your setup and save as settings.yaml:

| Value  | Required | Default | Description | 
| ------------- | ------------- | ------------- | ------------- |
| mqtt  | true | \ | Details of the MQTT broker
| mqtt:hostname  | true | \ | Hostname of the MQTT broker
| mqtt:port  | false | 1883 | Port of the MQTT broker
| mqtt:user | false | \ | The userlogin( if defined) for the MQTT broker
| mqtt:password | false | \ | the password ( if defined) for the MQTT broker
| deviceName | true | \ | device name is sent with topic
| client_id | true | \ | client id to connect to the MQTT broker
| timezone | true | \ | Your local timezone (you can find the list of timezones here: [time zones](https://gist.github.com/heyalexej/8bf688fd67d7199be4a1682b3eec7568))
| power_integer_state(Deprecated) | false | false | Deprecated
| update_interval | false | 60 | The update interval to send new values to the MQTT broker 
| sensors | false | \ | Enable/disable individual sensors (see example settings.yaml for how-to). Default is true for all sensors.

6. python3 src/system_sensors.py src/settings.yaml
7. (optional) create service to autostart the script at boot:
    1. sudo cp system_sensors.service /etc/systemd/system/system_sensors.service
    2. edit the path to your script path and settings.yaml. Also make sure you replace pi in "User=pi" with the account from which this script will be run. This is typically 'pi' on default raspbian system.
    4. sudo systemctl enable system_sensors.service 
    5. sudo systemctl start system_sensors.service

# Home Assistant configuration:
## Configuration:
The only config you need in Home Assistant is the following:
```yaml
mqtt:
  discovery: true
  discovery_prefix: homeassistant
```

## Lovelace UI example:
I have used following custom plugins for lovelace:
* vertical-stack-in-card
* mini-graph-card
* bar-card

Config:
```yaml
- type: 'custom:vertical-stack-in-card'
    title: Deconz System Monitor
    cards:
      - type: horizontal-stack
        cards:
          - type: custom:mini-graph-card
            entities:
              - sensor.deconz_cpu_usage
            name: CPU
            line_color: '#2980b9'
            line_width: 2
            hours_to_show: 24
          - type: custom:mini-graph-card
            entities:
              - sensor.deconz_temperature
            name: Temp
            line_color: '#2980b9'
            line_width: 2
            hours_to_show: 24
      - type: custom:bar-card
        entity: sensor.deconz_disk_use
        title: HDD
        title_position: inside
        align: split
        show_icon: true
        color: '#00ba6a'
      - type: custom:bar-card
        entity: sensor.deconz_memory_use
        title: RAM
        title_position: inside
        align: split
        show_icon: true
      - type: entities
        entities:
          - sensor.deconz_last_boot
          - sensor.deconz_under_voltage
```
Example:

![alt text](images/example.png?raw=true "Example")

[forum-shield]: https://img.shields.io/badge/community-forum-brightgreen.svg?style=for-the-badge
[forum]: https://community.home-assistant.io/t/remote-rpi-system-monitor/129274
[license-shield]: https://img.shields.io/github/license/sennevds/system_sensors.svg?style=for-the-badge
