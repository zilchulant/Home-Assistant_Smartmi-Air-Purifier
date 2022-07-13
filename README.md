# Home-Assistant Smartmi Air Purifier

How to get the Smartmi Air Purifier (zhimi.airpurifier.za1) working in Home Assistant with the help of HACS and Node-RED. This works with the current state of Home Assistant, Node-RED, HACS and the used github repositories.

- [Home-Assistant Smartmi Air Purifier](#home-assistant-smartmi-air-purifier)
  - [Necessary Addons & Tools](#necessary-addons--tools)
    - [Node-RED](#node-red)
    - [HACS](#hacs)
    - [Xiaomi Airpurifier Repo](#xiaomi-airpurifier-repo)
    - [Studio Code Server](#studio-code-server)
    - [Xiaomi Cloud Tokens Extractor](#xiaomi-cloud-tokens-extractor)
  - [Installing the fan](#installing-the-fan)
    - [Config](#config)
    - [Attributes](#attributes)
  - [Node-RED flow:](#node-red-flow)
  - [HA Helpers](#ha-helpers)
    - [Favorite level](#favorite-level)
  - [Switch Child lock](#switch-child-lock)
  - [Switch LED](#switch-led)


## Necessary Addons & Tools

### Node-RED

&nbsp; &nbsp; Default HA Addon

### HACS

&nbsp; &nbsp; To install HACS see [hacs.xyz](https://hacs.xyz)

### Xiaomi Airpurifier Repo

&nbsp; &nbsp; It's necessary to use the forked version from dameq1. This fork just adds the Smartmi fan, so other fans can be used as well. [github.com/dameq1/xiaomi_airpurifier_fork](https://github.com/dameq1/xiaomi_airpurifier_fork)

### Studio Code Server

&nbsp; &nbsp; Default Studio Code Server to edit the configuration.yaml

### Xiaomi Cloud Tokens Extractor

&nbsp; &nbsp; This tool is necessary to extract the Token: [github.com/PiotrMachowski/Xiaomi-cloud-tokens-extractor](https://github.com/PiotrMachowski/Xiaomi-cloud-tokens-extractor)

<br>

## Installing the fan

Just installing the fan with the extracted token by inserting the code in configuration.yaml <br> <br>
Now it is possible to turn the fan on and off. Also it is possible to switch the preset mode in context menu. It is also possible to see the values, but only as attributes. But it is not possible the change the favorite level, change the led screen brightness, or turn on/off the child lock nor the buzzer.

### Config
```
fan:
  - platform: xiaomi_miio_airpurifier
    name: Purifier 3c1
    host: <ip>
    token: <token>
    model: zhimi.airpurifier.za1
```

### Attributes
```
preset_modes: Auto, Silent, Favorite
preset_mode: null
model: zhimi.airpurifier.za1
temperature: 23.5
humidity: 56
TVOC: 33
PM25: 1
mode: 2
filter_hours_used: 56
filter_life_remaining: 98
favorite_level: 3
child_lock: false
motor_speed: 0
average_aqi: 3
purify_volume: 1304
use_time: 100381
buzzer: false
led_brightness: 2
filter_rfid_product_id: :::
filter_rfid_tag: ::::::
friendly_name: Purifier 3c
supported_features: 8
```

<br>

## Node-RED flow:

This flow reads the provided attributes and creates sensors with values. These are refreshed every 10 Seconds, or every hour, or with every change. It also reacts to helpers created in home assistant

<details><summary>Node-RED flow for Xiaomi Smartmi Fan</summary>
<p>

```
[
    {
        "id": "b0783ecb7cd0b347",
        "type": "api-current-state",
        "z": "d25643a6d08051c3",
        "name": "fan",
        "server": "d780e50948c91305",
        "version": 3,
        "outputs": 1,
        "halt_if": "",
        "halt_if_type": "str",
        "halt_if_compare": "is",
        "entity_id": "fan.purifier_fan",
        "state_type": "str",
        "blockInputOverrides": false,
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "entityState"
            },
            {
                "property": "data",
                "propertyType": "msg",
                "value": "",
                "valueType": "entity"
            }
        ],
        "for": "0",
        "forType": "num",
        "forUnits": "minutes",
        "override_topic": false,
        "state_location": "payload",
        "override_payload": "msg",
        "entity_location": "data",
        "override_data": "msg",
        "x": 1250,
        "y": 200,
        "wires": [
            [
                "63156436f51883e7",
                "6c1402f6e7dfa234",
                "a335a0bb5a48f1aa",
                "ed147c2b77c90dab",
                "3ad7a79db53d8947"
            ]
        ]
    },
    {
        "id": "f37e9a4d2535e83e",
        "type": "inject",
        "z": "d25643a6d08051c3",
        "name": "Alle 10s",
        "props": [
            {
                "p": "payload"
            },
            {
                "p": "topic",
                "vt": "str"
            }
        ],
        "repeat": "10",
        "crontab": "",
        "once": true,
        "onceDelay": "10",
        "topic": "",
        "payload": "",
        "payloadType": "date",
        "x": 1240,
        "y": 140,
        "wires": [
            [
                "b0783ecb7cd0b347"
            ]
        ]
    },
    {
        "id": "63156436f51883e7",
        "type": "ha-api",
        "z": "d25643a6d08051c3",
        "name": "nodered_fan_pm25",
        "server": "d780e50948c91305",
        "version": 1,
        "debugenabled": false,
        "protocol": "http",
        "method": "post",
        "path": "states/sensor.nodered_fan_pm25",
        "data": "(\t   {\t       \"state\": data.attributes.PM25,\t       \"attributes\": {\t           \"friendly_name\": \"Purifier fan PM 2,5\",\t           \"unit_of_measurement\": \"µm\",\t           \"icon\": \"mdi:air-purifier\"\t       }\t    }\t)\t\t",
        "dataType": "jsonata",
        "responseType": "json",
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "results"
            }
        ],
        "x": 1480,
        "y": 200,
        "wires": [
            []
        ]
    },
    {
        "id": "6c1402f6e7dfa234",
        "type": "ha-api",
        "z": "d25643a6d08051c3",
        "name": "nodered_fan_temperature",
        "server": "d780e50948c91305",
        "version": 1,
        "debugenabled": false,
        "protocol": "http",
        "method": "post",
        "path": "states/sensor.nodered_fan_temperature",
        "data": "(\t   {\t       \"state\": data.attributes.temperature,\t       \"attributes\":\t{\t           \"friendly_name\": \"Purifier fan Temperature\",\t           \"unit_of_measurement\": \"°C\",\t           \"icon\": \"mdi:thermometer\"\t}\t}\t)",
        "dataType": "jsonata",
        "responseType": "json",
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "results"
            }
        ],
        "x": 1500,
        "y": 260,
        "wires": [
            []
        ]
    },
    {
        "id": "a335a0bb5a48f1aa",
        "type": "ha-api",
        "z": "d25643a6d08051c3",
        "name": "nodered_fan_humidity",
        "server": "d780e50948c91305",
        "version": 1,
        "debugenabled": false,
        "protocol": "http",
        "method": "post",
        "path": "states/sensor.nodered_fan_humidity",
        "data": "(\t   {\t       \"state\": data.attributes.humidity,\t       \"attributes\": {\t           \"friendly_name\": \"Purifier fan Humidity\",\t           \"unit_of_measurement\": \"%\",\t           \"icon\": \"mdi:water-percent\"        \t       }     \t   } \t)",
        "dataType": "jsonata",
        "responseType": "json",
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "results"
            }
        ],
        "x": 1480,
        "y": 320,
        "wires": [
            []
        ]
    },
    {
        "id": "ed147c2b77c90dab",
        "type": "ha-api",
        "z": "d25643a6d08051c3",
        "name": "nodered_fan_tvoc",
        "server": "d780e50948c91305",
        "version": 1,
        "debugenabled": false,
        "protocol": "http",
        "method": "post",
        "path": "states/sensor.nodered_fan_tvoc",
        "data": "(\t   {\t       \"state\": data.attributes.TVOC,\t       \"attributes\": {\t           \"friendly_name\": \"Purifier fan TVOC\",\t           \"unit_of_measurement\": \"\",\t           \"icon\": \"mdi:heart\"        \t       }     \t   } \t)",
        "dataType": "jsonata",
        "responseType": "json",
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "results"
            }
        ],
        "x": 1470,
        "y": 380,
        "wires": [
            []
        ]
    },
    {
        "id": "3ad7a79db53d8947",
        "type": "ha-api",
        "z": "d25643a6d08051c3",
        "name": "nodered_fan_motor_speed",
        "server": "d780e50948c91305",
        "version": 1,
        "debugenabled": false,
        "protocol": "http",
        "method": "post",
        "path": "states/sensor.nodered_fan_motor_speed",
        "data": "(\t   {\t       \"state\": data.attributes.motor_speed,\t       \"attributes\": {\t           \"friendly_name\": \"Purifier fan Motor Speed\",\t           \"unit_of_measurement\": \"rpm\",\t           \"icon\": \"mdi:sync\"        \t       }     \t   } \t)",
        "dataType": "jsonata",
        "responseType": "json",
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "results"
            }
        ],
        "x": 1500,
        "y": 440,
        "wires": [
            []
        ]
    },
    {
        "id": "01b247835b6902e3",
        "type": "api-current-state",
        "z": "d25643a6d08051c3",
        "name": "fan",
        "server": "d780e50948c91305",
        "version": 3,
        "outputs": 1,
        "halt_if": "",
        "halt_if_type": "str",
        "halt_if_compare": "is",
        "entity_id": "fan.purifier_fan",
        "state_type": "str",
        "blockInputOverrides": false,
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "entityState"
            },
            {
                "property": "data",
                "propertyType": "msg",
                "value": "",
                "valueType": "entity"
            }
        ],
        "for": "0",
        "forType": "num",
        "forUnits": "minutes",
        "override_topic": false,
        "state_location": "payload",
        "override_payload": "msg",
        "entity_location": "data",
        "override_data": "msg",
        "x": 1790,
        "y": 200,
        "wires": [
            [
                "78075daace05159c",
                "343f38e16bf15512",
                "1d25642b1aa41b13"
            ]
        ]
    },
    {
        "id": "ccd1271e5be47876",
        "type": "inject",
        "z": "d25643a6d08051c3",
        "name": "Alle 1h",
        "props": [
            {
                "p": "payload"
            },
            {
                "p": "topic",
                "vt": "str"
            }
        ],
        "repeat": "3600",
        "crontab": "",
        "once": true,
        "onceDelay": "10",
        "topic": "",
        "payload": "",
        "payloadType": "date",
        "x": 1780,
        "y": 140,
        "wires": [
            [
                "01b247835b6902e3"
            ]
        ]
    },
    {
        "id": "78075daace05159c",
        "type": "ha-api",
        "z": "d25643a6d08051c3",
        "name": "nodered_fan_filter_hours_used",
        "server": "d780e50948c91305",
        "version": 1,
        "debugenabled": false,
        "protocol": "http",
        "method": "post",
        "path": "states/sensor.nodered_fan_filter_hours_used",
        "data": "(\t   {\t       \"state\": data.attributes.filter_hours_used,\t       \"attributes\": {\t           \"friendly_name\": \"Purifier fan Filter hourse used\",\t           \"unit_of_measurement\": \"h\",\t           \"icon\": \"mdi:clock\"        \t       }     \t   } \t)",
        "dataType": "jsonata",
        "responseType": "json",
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "results"
            }
        ],
        "x": 2050,
        "y": 200,
        "wires": [
            []
        ]
    },
    {
        "id": "343f38e16bf15512",
        "type": "ha-api",
        "z": "d25643a6d08051c3",
        "name": "nodered_fan_filter_life_remaining",
        "server": "d780e50948c91305",
        "version": 1,
        "debugenabled": false,
        "protocol": "http",
        "method": "post",
        "path": "states/sensor.nodered_fan_filter_life_remaining",
        "data": "(\t   {\t       \"state\": data.attributes.filter_life_remaining,\t       \"attributes\": {\t           \"friendly_name\": \"Purifier fan Filter Life Remaining\",\t           \"unit_of_measurement\": \"%\",\t           \"icon\": \"mdi:alert-box-outline\"        \t       }     \t   } \t)",
        "dataType": "jsonata",
        "responseType": "json",
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "results"
            }
        ],
        "x": 2060,
        "y": 260,
        "wires": [
            []
        ]
    },
    {
        "id": "f8d827d17ef11e60",
        "type": "ha-api",
        "z": "d25643a6d08051c3",
        "name": "nodered_fan_use_time",
        "server": "d780e50948c91305",
        "version": 1,
        "debugenabled": false,
        "protocol": "http",
        "method": "post",
        "path": "states/sensor.nodered_fan_use_time",
        "data": "(\t   {\t       \"state\": msg.use_time,\t       \"attributes\": {\t           \"friendly_name\": \"Purifier fan Use Time\",\t           \"unit_of_measurement\": \"h\",\t           \"icon\": \"mdi:clock-outline\"        \t       }     \t   } \t)",
        "dataType": "jsonata",
        "responseType": "json",
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "results"
            }
        ],
        "x": 2190,
        "y": 320,
        "wires": [
            []
        ]
    },
    {
        "id": "1d25642b1aa41b13",
        "type": "function",
        "z": "d25643a6d08051c3",
        "name": "",
        "func": "msg.use_time = ((msg.data.attributes.use_time / 3600).toFixed(2));\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 1980,
        "y": 320,
        "wires": [
            [
                "f8d827d17ef11e60"
            ]
        ]
    },
    {
        "id": "5d4425b026b1e36f",
        "type": "api-current-state",
        "z": "d25643a6d08051c3",
        "name": "fan",
        "server": "d780e50948c91305",
        "version": 3,
        "outputs": 1,
        "halt_if": "",
        "halt_if_type": "str",
        "halt_if_compare": "is",
        "entity_id": "fan.purifier_fan",
        "state_type": "str",
        "blockInputOverrides": false,
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "entityState"
            },
            {
                "property": "data",
                "propertyType": "msg",
                "value": "",
                "valueType": "entity"
            }
        ],
        "for": "0",
        "forType": "num",
        "forUnits": "minutes",
        "override_topic": false,
        "state_location": "payload",
        "override_payload": "msg",
        "entity_location": "data",
        "override_data": "msg",
        "x": 290,
        "y": 140,
        "wires": [
            [
                "e97a2e66c0446a04",
                "84d8d91354a0814a",
                "dbb84140535969a2",
                "e39dc10cff47b6d1",
                "cab59f83875ef13c",
                "462664695f934372",
                "625d9cdfa21577a5",
                "c00ef96874427671",
                "c9a01d579fc00e96",
                "c3b99e20900a4c46",
                "d31281bfa9bf73d8",
                "a6de26fb963fefdd"
            ]
        ]
    },
    {
        "id": "a6de26fb963fefdd",
        "type": "debug",
        "z": "d25643a6d08051c3",
        "name": "",
        "active": true,
        "tosidebar": true,
        "console": false,
        "tostatus": false,
        "complete": "true",
        "targetType": "full",
        "statusVal": "",
        "statusType": "auto",
        "x": 470,
        "y": 80,
        "wires": []
    },
    {
        "id": "fbca1c14d7991533",
        "type": "inject",
        "z": "d25643a6d08051c3",
        "name": "",
        "props": [
            {
                "p": "payload"
            },
            {
                "p": "topic",
                "vt": "str"
            }
        ],
        "repeat": "",
        "crontab": "",
        "once": true,
        "onceDelay": "10",
        "topic": "",
        "payload": "",
        "payloadType": "date",
        "x": 130,
        "y": 140,
        "wires": [
            [
                "5d4425b026b1e36f"
            ]
        ]
    },
    {
        "id": "e97a2e66c0446a04",
        "type": "ha-api",
        "z": "d25643a6d08051c3",
        "name": "nodered_fan_pm25",
        "server": "d780e50948c91305",
        "version": 1,
        "debugenabled": false,
        "protocol": "http",
        "method": "post",
        "path": "states/sensor.nodered_fan_pm25",
        "data": "(\t   {\t       \"state\": data.attributes.PM25,\t       \"attributes\": {\t           \"friendly_name\": \"Purifier fan PM 2,5\",\t           \"unit_of_measurement\": \"µm\",\t           \"icon\": \"mdi:air-purifier\"\t       }\t    }\t)\t\t",
        "dataType": "jsonata",
        "responseType": "json",
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "results"
            }
        ],
        "x": 520,
        "y": 140,
        "wires": [
            []
        ]
    },
    {
        "id": "84d8d91354a0814a",
        "type": "ha-api",
        "z": "d25643a6d08051c3",
        "name": "nodered_fan_temperature",
        "server": "d780e50948c91305",
        "version": 1,
        "debugenabled": false,
        "protocol": "http",
        "method": "post",
        "path": "states/sensor.nodered_fan_temperature",
        "data": "(\t   {\t       \"state\": data.attributes.temperature,\t       \"attributes\":\t{\t           \"friendly_name\": \"Purifier fan Temperature\",\t           \"unit_of_measurement\": \"°C\",\t           \"icon\": \"mdi:thermometer\"\t}\t}\t)",
        "dataType": "jsonata",
        "responseType": "json",
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "results"
            }
        ],
        "x": 540,
        "y": 200,
        "wires": [
            []
        ]
    },
    {
        "id": "dbb84140535969a2",
        "type": "ha-api",
        "z": "d25643a6d08051c3",
        "name": "nodered_fan_humidity",
        "server": "d780e50948c91305",
        "version": 1,
        "debugenabled": false,
        "protocol": "http",
        "method": "post",
        "path": "states/sensor.nodered_fan_humidity",
        "data": "(\t   {\t       \"state\": data.attributes.humidity,\t       \"attributes\": {\t           \"friendly_name\": \"Purifier fan Humidity\",\t           \"unit_of_measurement\": \"%\",\t           \"icon\": \"mdi:water-percent\"        \t       }     \t   } \t)",
        "dataType": "jsonata",
        "responseType": "json",
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "results"
            }
        ],
        "x": 520,
        "y": 260,
        "wires": [
            []
        ]
    },
    {
        "id": "e39dc10cff47b6d1",
        "type": "ha-api",
        "z": "d25643a6d08051c3",
        "name": "nodered_fan_tvoc",
        "server": "d780e50948c91305",
        "version": 1,
        "debugenabled": false,
        "protocol": "http",
        "method": "post",
        "path": "states/sensor.nodered_fan_tvoc",
        "data": "(\t   {\t       \"state\": data.attributes.TVOC,\t       \"attributes\": {\t           \"friendly_name\": \"Purifier fan TVOC\",\t           \"unit_of_measurement\": \"\",\t           \"icon\": \"mdi:heart\"        \t       }     \t   } \t)",
        "dataType": "jsonata",
        "responseType": "json",
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "results"
            }
        ],
        "x": 510,
        "y": 320,
        "wires": [
            []
        ]
    },
    {
        "id": "cab59f83875ef13c",
        "type": "ha-api",
        "z": "d25643a6d08051c3",
        "name": "nodered_fan_filter_hours_used",
        "server": "d780e50948c91305",
        "version": 1,
        "debugenabled": false,
        "protocol": "http",
        "method": "post",
        "path": "states/sensor.nodered_fan_filter_hours_used",
        "data": "(\t   {\t       \"state\": data.attributes.filter_hours_used,\t       \"attributes\": {\t           \"friendly_name\": \"Purifier fan Filter hourse used\",\t           \"unit_of_measurement\": \"h\",\t           \"icon\": \"mdi:clock\"        \t       }     \t   } \t)",
        "dataType": "jsonata",
        "responseType": "json",
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "results"
            }
        ],
        "x": 550,
        "y": 380,
        "wires": [
            []
        ]
    },
    {
        "id": "462664695f934372",
        "type": "ha-api",
        "z": "d25643a6d08051c3",
        "name": "nodered_fan_filter_life_remaining",
        "server": "d780e50948c91305",
        "version": 1,
        "debugenabled": false,
        "protocol": "http",
        "method": "post",
        "path": "states/sensor.nodered_fan_filter_life_remaining",
        "data": "(\t   {\t       \"state\": data.attributes.filter_life_remaining,\t       \"attributes\": {\t           \"friendly_name\": \"Purifier fan Filter Life Remaining\",\t           \"unit_of_measurement\": \"%\",\t           \"icon\": \"mdi:alert-box-outline\"        \t       }     \t   } \t)",
        "dataType": "jsonata",
        "responseType": "json",
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "results"
            }
        ],
        "x": 560,
        "y": 440,
        "wires": [
            []
        ]
    },
    {
        "id": "625d9cdfa21577a5",
        "type": "ha-api",
        "z": "d25643a6d08051c3",
        "name": "nodered_fan_motor_speed",
        "server": "d780e50948c91305",
        "version": 1,
        "debugenabled": false,
        "protocol": "http",
        "method": "post",
        "path": "states/sensor.nodered_fan_motor_speed",
        "data": "(\t   {\t       \"state\": data.attributes.motor_speed,\t       \"attributes\": {\t           \"friendly_name\": \"Purifier fan Motor Speed\",\t           \"unit_of_measurement\": \"rpm\",\t           \"icon\": \"mdi:sync\"        \t       }     \t   } \t)",
        "dataType": "jsonata",
        "responseType": "json",
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "results"
            }
        ],
        "x": 540,
        "y": 500,
        "wires": [
            []
        ]
    },
    {
        "id": "876c8849cb28728d",
        "type": "ha-api",
        "z": "d25643a6d08051c3",
        "name": "nodered_fan_use_time",
        "server": "d780e50948c91305",
        "version": 1,
        "debugenabled": false,
        "protocol": "http",
        "method": "post",
        "path": "states/sensor.nodered_fan_use_time",
        "data": "(\t   {\t       \"state\": msg.use_time,\t       \"attributes\": {\t           \"friendly_name\": \"Purifier fan Use Time\",\t           \"unit_of_measurement\": \"h\",\t           \"icon\": \"mdi:clock-outline\"        \t       }     \t   } \t)",
        "dataType": "jsonata",
        "responseType": "json",
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "results"
            }
        ],
        "x": 690,
        "y": 560,
        "wires": [
            []
        ]
    },
    {
        "id": "c00ef96874427671",
        "type": "function",
        "z": "d25643a6d08051c3",
        "name": "",
        "func": "msg.use_time = ((msg.data.attributes.use_time / 3600).toFixed(2));\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 480,
        "y": 560,
        "wires": [
            [
                "876c8849cb28728d"
            ]
        ]
    },
    {
        "id": "d518bf48a4b66715",
        "type": "ha-api",
        "z": "d25643a6d08051c3",
        "name": "nodered_fan_led_brightness",
        "server": "d780e50948c91305",
        "version": 1,
        "debugenabled": false,
        "protocol": "http",
        "method": "post",
        "path": "states/sensor.nodered_fan_led_brightness",
        "data": "(\t   {\t       \"state\": msg.payload ,\t       \"attributes\": {\t           \"friendly_name\": \"Purifier fan LED Brightness\",\t           \"icon\": \"mdi:lightbulb\"\t       }\t    }\t)",
        "dataType": "jsonata",
        "responseType": "json",
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "results"
            }
        ],
        "x": 920,
        "y": 620,
        "wires": [
            []
        ]
    },
    {
        "id": "c9a01d579fc00e96",
        "type": "ha-api",
        "z": "d25643a6d08051c3",
        "name": "nodered_fan_favorite_level",
        "server": "d780e50948c91305",
        "version": 1,
        "debugenabled": false,
        "protocol": "http",
        "method": "post",
        "path": "states/sensor.nodered_fan_favorite_level",
        "data": "(\t   {\t       \"state\": data.attributes.favorite_level,\t       \"attributes\": {\t           \"friendly_name\": \"Purifier fan Favorite Level\",\t           \"icon\": \"mdi:star-box-multiple\"\t       }\t    }\t)",
        "dataType": "jsonata",
        "responseType": "json",
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "results"
            }
        ],
        "x": 540,
        "y": 680,
        "wires": [
            []
        ]
    },
    {
        "id": "87e478a3351ddd1f",
        "type": "function",
        "z": "d25643a6d08051c3",
        "name": "",
        "func": "var payload = msg.payload;\nif (payload == \"0\") {\n  msg.payload = \"low\";\n} else if (payload == \"1\") {\n  msg.payload = \"low\";\n} else if (payload == \"2\") {\n  msg.payload = \"off\";\n} else {\n  msg.payload = char(payload);\n}\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 700,
        "y": 620,
        "wires": [
            [
                "d518bf48a4b66715"
            ]
        ]
    },
    {
        "id": "c3b99e20900a4c46",
        "type": "change",
        "z": "d25643a6d08051c3",
        "name": "",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "data.attributes.led_brightness",
                "tot": "jsonata"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 510,
        "y": 620,
        "wires": [
            [
                "87e478a3351ddd1f"
            ]
        ]
    },
    {
        "id": "d31281bfa9bf73d8",
        "type": "switch",
        "z": "d25643a6d08051c3",
        "name": "",
        "property": "payload",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "off",
                "vt": "str"
            },
            {
                "t": "else"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 2,
        "x": 470,
        "y": 740,
        "wires": [
            [
                "779f381d813e8f4f"
            ],
            [
                "0aeeeff30c74b81a"
            ]
        ]
    },
    {
        "id": "779f381d813e8f4f",
        "type": "ha-api",
        "z": "d25643a6d08051c3",
        "name": "nodered_fan_preset_mode",
        "server": "d780e50948c91305",
        "version": 1,
        "debugenabled": false,
        "protocol": "http",
        "method": "post",
        "path": "states/sensor.nodered_fan_preset_mode",
        "data": "(\t   {\t       \"state\": \"aus\" ,\t       \"attributes\": {\t           \"friendly_name\": \"Purifier fan Preset Mode\",\t           \"icon\": \"mdi:card-multiple\"\t       }\t    }\t)",
        "dataType": "jsonata",
        "responseType": "json",
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "results"
            }
        ],
        "x": 760,
        "y": 740,
        "wires": [
            []
        ]
    },
    {
        "id": "0aeeeff30c74b81a",
        "type": "ha-api",
        "z": "d25643a6d08051c3",
        "name": "nodered_fan_preset_mode",
        "server": "d780e50948c91305",
        "version": 1,
        "debugenabled": false,
        "protocol": "http",
        "method": "post",
        "path": "states/sensor.nodered_fan_preset_mode",
        "data": "(\t   {\t       \"state\": data.attributes.preset_mode ,\t       \"attributes\": {\t           \"friendly_name\": \"Purifier fan Preset Mode\",\t           \"icon\": \"mdi:card-multiple\"\t       }\t    }\t)",
        "dataType": "jsonata",
        "responseType": "json",
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "results"
            }
        ],
        "x": 760,
        "y": 800,
        "wires": [
            []
        ]
    },
    {
        "id": "4ea800fc81f0914b",
        "type": "api-current-state",
        "z": "d25643a6d08051c3",
        "name": "fan",
        "server": "d780e50948c91305",
        "version": 3,
        "outputs": 1,
        "halt_if": "",
        "halt_if_type": "str",
        "halt_if_compare": "is",
        "entity_id": "fan.purifier_fan",
        "state_type": "str",
        "blockInputOverrides": false,
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "entityState"
            },
            {
                "property": "data",
                "propertyType": "msg",
                "value": "",
                "valueType": "entity"
            }
        ],
        "for": "0",
        "forType": "num",
        "forUnits": "minutes",
        "override_topic": false,
        "state_location": "payload",
        "override_payload": "msg",
        "entity_location": "data",
        "override_data": "msg",
        "x": 2670,
        "y": 200,
        "wires": [
            [
                "ae35693b0607b1c7",
                "70537ff10a24e0b5",
                "b00c2324c0a2e126"
            ]
        ]
    },
    {
        "id": "bc33556d6dd927bc",
        "type": "inject",
        "z": "d25643a6d08051c3",
        "name": "Event Change",
        "props": [
            {
                "p": "payload"
            },
            {
                "p": "topic",
                "vt": "str"
            }
        ],
        "repeat": "",
        "crontab": "00 03 * * *",
        "once": false,
        "onceDelay": "10",
        "topic": "",
        "payload": "",
        "payloadType": "date",
        "x": 2440,
        "y": 140,
        "wires": [
            [
                "7eacec2dce65df25"
            ]
        ]
    },
    {
        "id": "be36078d8f5d936b",
        "type": "ha-api",
        "z": "d25643a6d08051c3",
        "name": "nodered_fan_led_brightness",
        "server": "d780e50948c91305",
        "version": 1,
        "debugenabled": false,
        "protocol": "http",
        "method": "post",
        "path": "states/sensor.nodered_fan_led_brightness",
        "data": "(\t   {\t       \"state\": msg.payload ,\t       \"attributes\": {\t           \"friendly_name\": \"Purifier fan LED Brightness\",\t           \"icon\": \"mdi:lightbulb\"\t       }\t    }\t)",
        "dataType": "jsonata",
        "responseType": "json",
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "results"
            }
        ],
        "x": 3300,
        "y": 200,
        "wires": [
            []
        ]
    },
    {
        "id": "ae35693b0607b1c7",
        "type": "ha-api",
        "z": "d25643a6d08051c3",
        "name": "nodered_fan_favorite_level",
        "server": "d780e50948c91305",
        "version": 1,
        "debugenabled": false,
        "protocol": "http",
        "method": "post",
        "path": "states/sensor.nodered_fan_favorite_level",
        "data": "(\t   {\t       \"state\": data.attributes.favorite_level,\t       \"attributes\": {\t           \"friendly_name\": \"Purifier fan Favorite Level\",\t           \"icon\": \"mdi:star-box-multiple\"\t       }\t    }\t)",
        "dataType": "jsonata",
        "responseType": "json",
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "results"
            }
        ],
        "x": 2920,
        "y": 260,
        "wires": [
            []
        ]
    },
    {
        "id": "e47aeb7c221f301c",
        "type": "function",
        "z": "d25643a6d08051c3",
        "name": "",
        "func": "var payload = msg.payload;\nif (payload == \"0\") {\n  msg.payload = \"high\";\n} else if (payload == \"1\") {\n  msg.payload = \"low\";\n} else if (payload == \"2\") {\n  msg.payload = \"off\";\n} else {\n  msg.payload = char(payload);\n}\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 3080,
        "y": 200,
        "wires": [
            [
                "be36078d8f5d936b"
            ]
        ]
    },
    {
        "id": "70537ff10a24e0b5",
        "type": "change",
        "z": "d25643a6d08051c3",
        "name": "",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "data.attributes.led_brightness",
                "tot": "jsonata"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 2890,
        "y": 200,
        "wires": [
            [
                "e47aeb7c221f301c"
            ]
        ]
    },
    {
        "id": "b00c2324c0a2e126",
        "type": "switch",
        "z": "d25643a6d08051c3",
        "name": "",
        "property": "payload",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "off",
                "vt": "str"
            },
            {
                "t": "else"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 2,
        "x": 2850,
        "y": 320,
        "wires": [
            [
                "edf435b0b90e98e9"
            ],
            [
                "0276e67a9c9dd478"
            ]
        ]
    },
    {
        "id": "edf435b0b90e98e9",
        "type": "ha-api",
        "z": "d25643a6d08051c3",
        "name": "nodered_fan_preset_mode",
        "server": "d780e50948c91305",
        "version": 1,
        "debugenabled": false,
        "protocol": "http",
        "method": "post",
        "path": "states/sensor.nodered_fan_preset_mode",
        "data": "(\t   {\t       \"state\": \"aus\" ,\t       \"attributes\": {\t           \"friendly_name\": \"Purifier fan Preset Mode\",\t           \"icon\": \"mdi:card-multiple\"\t       }\t    }\t)",
        "dataType": "jsonata",
        "responseType": "json",
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "results"
            }
        ],
        "x": 3140,
        "y": 320,
        "wires": [
            []
        ]
    },
    {
        "id": "0276e67a9c9dd478",
        "type": "ha-api",
        "z": "d25643a6d08051c3",
        "name": "nodered_fan_preset_mode",
        "server": "d780e50948c91305",
        "version": 1,
        "debugenabled": false,
        "protocol": "http",
        "method": "post",
        "path": "states/sensor.nodered_fan_preset_mode",
        "data": "(\t   {\t       \"state\": data.attributes.preset_mode ,\t       \"attributes\": {\t           \"friendly_name\": \"Purifier fan Preset Mode\",\t           \"icon\": \"mdi:card-multiple\"\t       }\t    }\t)",
        "dataType": "jsonata",
        "responseType": "json",
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "results"
            }
        ],
        "x": 3140,
        "y": 380,
        "wires": [
            []
        ]
    },
    {
        "id": "1cf05186c51dfc6f",
        "type": "server-state-changed",
        "z": "d25643a6d08051c3",
        "name": "",
        "server": "d780e50948c91305",
        "version": 4,
        "exposeToHomeAssistant": false,
        "haConfig": [
            {
                "property": "name",
                "value": ""
            },
            {
                "property": "icon",
                "value": ""
            }
        ],
        "entityidfilter": "fan.purifier_fan",
        "entityidfiltertype": "exact",
        "outputinitially": false,
        "state_type": "str",
        "haltifstate": "",
        "halt_if_type": "str",
        "halt_if_compare": "is",
        "outputs": 1,
        "output_only_on_state_change": true,
        "for": "0",
        "forType": "num",
        "forUnits": "minutes",
        "ignorePrevStateNull": false,
        "ignorePrevStateUnknown": false,
        "ignorePrevStateUnavailable": false,
        "ignoreCurrentStateUnknown": false,
        "ignoreCurrentStateUnavailable": false,
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "entityState"
            },
            {
                "property": "data",
                "propertyType": "msg",
                "value": "",
                "valueType": "eventData"
            },
            {
                "property": "topic",
                "propertyType": "msg",
                "value": "",
                "valueType": "triggerId"
            }
        ],
        "x": 2490,
        "y": 300,
        "wires": [
            [
                "7eacec2dce65df25"
            ]
        ]
    },
    {
        "id": "7a39fd3188263a72",
        "type": "server-state-changed",
        "z": "d25643a6d08051c3",
        "name": "",
        "server": "d780e50948c91305",
        "version": 4,
        "exposeToHomeAssistant": false,
        "haConfig": [
            {
                "property": "name",
                "value": ""
            },
            {
                "property": "icon",
                "value": ""
            }
        ],
        "entityidfilter": "sensor.nodered_fan_led_brightness",
        "entityidfiltertype": "exact",
        "outputinitially": false,
        "state_type": "str",
        "haltifstate": "",
        "halt_if_type": "str",
        "halt_if_compare": "is",
        "outputs": 1,
        "output_only_on_state_change": true,
        "for": "0",
        "forType": "num",
        "forUnits": "minutes",
        "ignorePrevStateNull": false,
        "ignorePrevStateUnknown": false,
        "ignorePrevStateUnavailable": false,
        "ignoreCurrentStateUnknown": false,
        "ignoreCurrentStateUnavailable": false,
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "entityState"
            },
            {
                "property": "data",
                "propertyType": "msg",
                "value": "",
                "valueType": "eventData"
            },
            {
                "property": "topic",
                "propertyType": "msg",
                "value": "",
                "valueType": "triggerId"
            }
        ],
        "x": 2550,
        "y": 360,
        "wires": [
            [
                "7eacec2dce65df25"
            ]
        ]
    },
    {
        "id": "ac080b90abb851ec",
        "type": "server-state-changed",
        "z": "d25643a6d08051c3",
        "name": "",
        "server": "d780e50948c91305",
        "version": 4,
        "exposeToHomeAssistant": false,
        "haConfig": [
            {
                "property": "name",
                "value": ""
            },
            {
                "property": "icon",
                "value": ""
            }
        ],
        "entityidfilter": "sensor.nodered_fan_favorite_level",
        "entityidfiltertype": "exact",
        "outputinitially": false,
        "state_type": "str",
        "haltifstate": "",
        "halt_if_type": "str",
        "halt_if_compare": "is",
        "outputs": 1,
        "output_only_on_state_change": true,
        "for": "0",
        "forType": "num",
        "forUnits": "minutes",
        "ignorePrevStateNull": false,
        "ignorePrevStateUnknown": false,
        "ignorePrevStateUnavailable": false,
        "ignoreCurrentStateUnknown": false,
        "ignoreCurrentStateUnavailable": false,
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "entityState"
            },
            {
                "property": "data",
                "propertyType": "msg",
                "value": "",
                "valueType": "eventData"
            },
            {
                "property": "topic",
                "propertyType": "msg",
                "value": "",
                "valueType": "triggerId"
            }
        ],
        "x": 2550,
        "y": 420,
        "wires": [
            [
                "7eacec2dce65df25"
            ]
        ]
    },
    {
        "id": "4228200366c7b07d",
        "type": "server-state-changed",
        "z": "d25643a6d08051c3",
        "name": "",
        "server": "d780e50948c91305",
        "version": 4,
        "exposeToHomeAssistant": false,
        "haConfig": [
            {
                "property": "name",
                "value": ""
            },
            {
                "property": "icon",
                "value": ""
            }
        ],
        "entityidfilter": "sensor.nodered_fan_preset_mode",
        "entityidfiltertype": "exact",
        "outputinitially": false,
        "state_type": "str",
        "haltifstate": "",
        "halt_if_type": "str",
        "halt_if_compare": "is",
        "outputs": 1,
        "output_only_on_state_change": true,
        "for": "0",
        "forType": "num",
        "forUnits": "minutes",
        "ignorePrevStateNull": false,
        "ignorePrevStateUnknown": false,
        "ignorePrevStateUnavailable": false,
        "ignoreCurrentStateUnknown": false,
        "ignoreCurrentStateUnavailable": false,
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "entityState"
            },
            {
                "property": "data",
                "propertyType": "msg",
                "value": "",
                "valueType": "eventData"
            },
            {
                "property": "topic",
                "propertyType": "msg",
                "value": "",
                "valueType": "triggerId"
            }
        ],
        "x": 2550,
        "y": 480,
        "wires": [
            [
                "7eacec2dce65df25"
            ]
        ]
    },
    {
        "id": "7eacec2dce65df25",
        "type": "delay",
        "z": "d25643a6d08051c3",
        "name": "",
        "pauseType": "delay",
        "timeout": "500",
        "timeoutUnits": "milliseconds",
        "rate": "1",
        "nbRateUnits": "1",
        "rateUnits": "second",
        "randomFirst": "1",
        "randomLast": "5",
        "randomUnits": "seconds",
        "drop": false,
        "allowrate": false,
        "outputs": 1,
        "x": 2460,
        "y": 200,
        "wires": [
            [
                "4ea800fc81f0914b"
            ]
        ]
    },
    {
        "id": "ea616ee0e54d1ffd",
        "type": "server-state-changed",
        "z": "d25643a6d08051c3",
        "name": "",
        "server": "d780e50948c91305",
        "version": 4,
        "exposeToHomeAssistant": false,
        "haConfig": [
            {
                "property": "name",
                "value": ""
            },
            {
                "property": "icon",
                "value": ""
            }
        ],
        "entityidfilter": "input_number.fan_favorite_level",
        "entityidfiltertype": "exact",
        "outputinitially": false,
        "state_type": "str",
        "haltifstate": "",
        "halt_if_type": "str",
        "halt_if_compare": "is",
        "outputs": 1,
        "output_only_on_state_change": true,
        "for": "0",
        "forType": "num",
        "forUnits": "minutes",
        "ignorePrevStateNull": false,
        "ignorePrevStateUnknown": false,
        "ignorePrevStateUnavailable": false,
        "ignoreCurrentStateUnknown": false,
        "ignoreCurrentStateUnavailable": false,
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "entityState"
            },
            {
                "property": "data",
                "propertyType": "msg",
                "value": "",
                "valueType": "eventData"
            },
            {
                "property": "topic",
                "propertyType": "msg",
                "value": "",
                "valueType": "triggerId"
            }
        ],
        "x": 260,
        "y": 920,
        "wires": [
            [
                "87dd717b970f7079"
            ]
        ]
    },
    {
        "id": "402ba2c1a4e0829b",
        "type": "api-call-service",
        "z": "d25643a6d08051c3",
        "name": "fan Favorite Level",
        "server": "d780e50948c91305",
        "version": 5,
        "debugenabled": false,
        "domain": "xiaomi_miio_airpurifier",
        "service": "fan_set_favorite_level",
        "areaId": [],
        "deviceId": [],
        "entityId": [
            "fan.purifier_fan"
        ],
        "data": "{\"level\":msg.payload}",
        "dataType": "jsonata",
        "mergeContext": "",
        "mustacheAltTags": false,
        "outputProperties": [],
        "queue": "none",
        "x": 830,
        "y": 920,
        "wires": [
            [
                "8b3fc2161e7352a9"
            ]
        ]
    },
    {
        "id": "87dd717b970f7079",
        "type": "function",
        "z": "d25643a6d08051c3",
        "name": "",
        "func": "msg.payload = parseInt(msg.payload);\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 560,
        "y": 920,
        "wires": [
            [
                "402ba2c1a4e0829b"
            ]
        ]
    },
    {
        "id": "ef03d9d4a1fe27d2",
        "type": "server-state-changed",
        "z": "d25643a6d08051c3",
        "name": "",
        "server": "d780e50948c91305",
        "version": 4,
        "exposeToHomeAssistant": false,
        "haConfig": [
            {
                "property": "name",
                "value": ""
            },
            {
                "property": "icon",
                "value": ""
            }
        ],
        "entityidfilter": "input_select.fan_modus",
        "entityidfiltertype": "exact",
        "outputinitially": false,
        "state_type": "str",
        "haltifstate": "",
        "halt_if_type": "str",
        "halt_if_compare": "is",
        "outputs": 1,
        "output_only_on_state_change": true,
        "for": "0",
        "forType": "num",
        "forUnits": "minutes",
        "ignorePrevStateNull": false,
        "ignorePrevStateUnknown": false,
        "ignorePrevStateUnavailable": false,
        "ignoreCurrentStateUnknown": false,
        "ignoreCurrentStateUnavailable": false,
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "entityState"
            },
            {
                "property": "data",
                "propertyType": "msg",
                "value": "",
                "valueType": "eventData"
            },
            {
                "property": "topic",
                "propertyType": "msg",
                "value": "",
                "valueType": "triggerId"
            }
        ],
        "x": 240,
        "y": 980,
        "wires": [
            [
                "3d129707dd76e537"
            ]
        ]
    },
    {
        "id": "3d129707dd76e537",
        "type": "api-call-service",
        "z": "d25643a6d08051c3",
        "name": "fan Favorite Level",
        "server": "d780e50948c91305",
        "version": 5,
        "debugenabled": true,
        "domain": "xiaomi_miio_airpurifier",
        "service": "fan_set_favorite_level",
        "areaId": [],
        "deviceId": [],
        "entityId": [],
        "data": "{\"level\":msg.payload}",
        "dataType": "jsonata",
        "mergeContext": "",
        "mustacheAltTags": false,
        "outputProperties": [],
        "queue": "none",
        "x": 590,
        "y": 980,
        "wires": [
            [
                "8b3fc2161e7352a9"
            ]
        ]
    },
    {
        "id": "651716b236ddfb61",
        "type": "server-state-changed",
        "z": "d25643a6d08051c3",
        "name": "",
        "server": "d780e50948c91305",
        "version": 4,
        "exposeToHomeAssistant": false,
        "haConfig": [
            {
                "property": "name",
                "value": ""
            },
            {
                "property": "icon",
                "value": ""
            }
        ],
        "entityidfilter": "input_select.fan_led",
        "entityidfiltertype": "exact",
        "outputinitially": false,
        "state_type": "str",
        "haltifstate": "",
        "halt_if_type": "str",
        "halt_if_compare": "is",
        "outputs": 1,
        "output_only_on_state_change": true,
        "for": "0",
        "forType": "num",
        "forUnits": "minutes",
        "ignorePrevStateNull": false,
        "ignorePrevStateUnknown": false,
        "ignorePrevStateUnavailable": false,
        "ignoreCurrentStateUnknown": false,
        "ignoreCurrentStateUnavailable": false,
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "entityState"
            },
            {
                "property": "data",
                "propertyType": "msg",
                "value": "",
                "valueType": "eventData"
            },
            {
                "property": "topic",
                "propertyType": "msg",
                "value": "",
                "valueType": "triggerId"
            }
        ],
        "x": 220,
        "y": 1040,
        "wires": [
            [
                "62a0acea84b77a62"
            ]
        ]
    },
    {
        "id": "62a0acea84b77a62",
        "type": "function",
        "z": "d25643a6d08051c3",
        "name": "",
        "func": "var payload = msg.payload;\nif (payload == \"high\") {\n  msg.payload = \"0\";\n} else if (payload == \"low\") {\n  msg.payload = \"1\";\n} else if (payload == \"off\") {\n  msg.payload = \"2\";\n} else {\n  msg.payload = char(payload);\n}\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 560,
        "y": 1040,
        "wires": [
            [
                "a78c3cbb62572337"
            ]
        ]
    },
    {
        "id": "a78c3cbb62572337",
        "type": "api-call-service",
        "z": "d25643a6d08051c3",
        "name": "fan LED",
        "server": "d780e50948c91305",
        "version": 5,
        "debugenabled": true,
        "domain": "xiaomi_miio_airpurifier",
        "service": "fan_set_led_brightness",
        "areaId": [],
        "deviceId": [],
        "entityId": [
            "fan.purifier_fan"
        ],
        "data": "{\"brightness\":msg.payload}",
        "dataType": "jsonata",
        "mergeContext": "",
        "mustacheAltTags": false,
        "outputProperties": [],
        "queue": "none",
        "x": 800,
        "y": 1040,
        "wires": [
            [
                "8b3fc2161e7352a9"
            ]
        ]
    },
    {
        "id": "3849f35c882ed499",
        "type": "server-state-changed",
        "z": "d25643a6d08051c3",
        "name": "",
        "server": "d780e50948c91305",
        "version": 4,
        "exposeToHomeAssistant": false,
        "haConfig": [
            {
                "property": "name",
                "value": ""
            },
            {
                "property": "icon",
                "value": ""
            }
        ],
        "entityidfilter": "input_boolean.fan_child_lock",
        "entityidfiltertype": "exact",
        "outputinitially": false,
        "state_type": "str",
        "haltifstate": "",
        "halt_if_type": "str",
        "halt_if_compare": "is",
        "outputs": 1,
        "output_only_on_state_change": true,
        "for": "0",
        "forType": "num",
        "forUnits": "minutes",
        "ignorePrevStateNull": false,
        "ignorePrevStateUnknown": false,
        "ignorePrevStateUnavailable": false,
        "ignoreCurrentStateUnknown": false,
        "ignoreCurrentStateUnavailable": false,
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "entityState"
            },
            {
                "property": "data",
                "propertyType": "msg",
                "value": "",
                "valueType": "eventData"
            },
            {
                "property": "topic",
                "propertyType": "msg",
                "value": "",
                "valueType": "triggerId"
            }
        ],
        "x": 250,
        "y": 1100,
        "wires": [
            [
                "7f53bf4d977006c0"
            ]
        ]
    },
    {
        "id": "548894990b541dea",
        "type": "api-call-service",
        "z": "d25643a6d08051c3",
        "name": "fan Child Lock off",
        "server": "d780e50948c91305",
        "version": 5,
        "debugenabled": true,
        "domain": "xiaomi_miio_airpurifier",
        "service": "fan_set_child_lock_off",
        "areaId": [],
        "deviceId": [],
        "entityId": [
            "fan.purifier_fan"
        ],
        "data": "",
        "dataType": "jsonata",
        "mergeContext": "",
        "mustacheAltTags": false,
        "outputProperties": [],
        "queue": "none",
        "x": 830,
        "y": 1160,
        "wires": [
            [
                "8b3fc2161e7352a9"
            ]
        ]
    },
    {
        "id": "7f53bf4d977006c0",
        "type": "switch",
        "z": "d25643a6d08051c3",
        "name": "",
        "property": "payload",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "on",
                "vt": "str"
            },
            {
                "t": "eq",
                "v": "off",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 2,
        "x": 550,
        "y": 1100,
        "wires": [
            [
                "073d039ce7a80e49"
            ],
            [
                "548894990b541dea"
            ]
        ]
    },
    {
        "id": "073d039ce7a80e49",
        "type": "api-call-service",
        "z": "d25643a6d08051c3",
        "name": "fan Child Lock on",
        "server": "d780e50948c91305",
        "version": 5,
        "debugenabled": true,
        "domain": "xiaomi_miio_airpurifier",
        "service": "fan_set_child_lock_on",
        "areaId": [],
        "deviceId": [],
        "entityId": [
            "fan.purifier_fan"
        ],
        "data": "",
        "dataType": "jsonata",
        "mergeContext": "",
        "mustacheAltTags": false,
        "outputProperties": [],
        "queue": "none",
        "x": 830,
        "y": 1100,
        "wires": [
            [
                "8b3fc2161e7352a9"
            ]
        ]
    },
    {
        "id": "3333ecea379bbfdb",
        "type": "server-state-changed",
        "z": "d25643a6d08051c3",
        "name": "",
        "server": "d780e50948c91305",
        "version": 4,
        "exposeToHomeAssistant": false,
        "haConfig": [
            {
                "property": "name",
                "value": ""
            },
            {
                "property": "icon",
                "value": ""
            }
        ],
        "entityidfilter": "input_boolean.fan_buzzer",
        "entityidfiltertype": "exact",
        "outputinitially": false,
        "state_type": "str",
        "haltifstate": "",
        "halt_if_type": "str",
        "halt_if_compare": "is",
        "outputs": 1,
        "output_only_on_state_change": true,
        "for": "0",
        "forType": "num",
        "forUnits": "minutes",
        "ignorePrevStateNull": false,
        "ignorePrevStateUnknown": false,
        "ignorePrevStateUnavailable": false,
        "ignoreCurrentStateUnknown": false,
        "ignoreCurrentStateUnavailable": false,
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "entityState"
            },
            {
                "property": "data",
                "propertyType": "msg",
                "value": "",
                "valueType": "eventData"
            },
            {
                "property": "topic",
                "propertyType": "msg",
                "value": "",
                "valueType": "triggerId"
            }
        ],
        "x": 240,
        "y": 1220,
        "wires": [
            [
                "44d4bf5a45261624"
            ]
        ]
    },
    {
        "id": "e77d7d6d08e324ef",
        "type": "api-call-service",
        "z": "d25643a6d08051c3",
        "name": "fan Buzzer off",
        "server": "d780e50948c91305",
        "version": 5,
        "debugenabled": true,
        "domain": "xiaomi_miio_airpurifier",
        "service": "fan_set_buzzer_off",
        "areaId": [],
        "deviceId": [],
        "entityId": [
            "fan.purifier_fan"
        ],
        "data": "",
        "dataType": "jsonata",
        "mergeContext": "",
        "mustacheAltTags": false,
        "outputProperties": [],
        "queue": "none",
        "x": 820,
        "y": 1280,
        "wires": [
            [
                "8b3fc2161e7352a9"
            ]
        ]
    },
    {
        "id": "44d4bf5a45261624",
        "type": "switch",
        "z": "d25643a6d08051c3",
        "name": "",
        "property": "payload",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "on",
                "vt": "str"
            },
            {
                "t": "eq",
                "v": "off",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 2,
        "x": 550,
        "y": 1220,
        "wires": [
            [
                "8b20c7af0342cf1c"
            ],
            [
                "e77d7d6d08e324ef"
            ]
        ]
    },
    {
        "id": "8b20c7af0342cf1c",
        "type": "api-call-service",
        "z": "d25643a6d08051c3",
        "name": "fan Buzzer on",
        "server": "d780e50948c91305",
        "version": 5,
        "debugenabled": true,
        "domain": "xiaomi_miio_airpurifier",
        "service": "fan_set_buzzer_on",
        "areaId": [],
        "deviceId": [],
        "entityId": [
            "fan.purifier_fan"
        ],
        "data": "",
        "dataType": "jsonata",
        "mergeContext": "",
        "mustacheAltTags": false,
        "outputProperties": [],
        "queue": "none",
        "x": 820,
        "y": 1220,
        "wires": [
            [
                "8b3fc2161e7352a9"
            ]
        ]
    },
    {
        "id": "283ea41db99304c3",
        "type": "link in",
        "z": "d25643a6d08051c3",
        "name": "",
        "links": [
            "8b3fc2161e7352a9"
        ],
        "x": 2605,
        "y": 140,
        "wires": [
            [
                "22da5afa7a176845"
            ]
        ]
    },
    {
        "id": "8b3fc2161e7352a9",
        "type": "link out",
        "z": "d25643a6d08051c3",
        "name": "",
        "mode": "link",
        "links": [
            "283ea41db99304c3",
            "7dc6cb40b962515d",
            "a075be3dde31eb3c",
            "5dc310df70525062"
        ],
        "x": 1035,
        "y": 920,
        "wires": []
    },
    {
        "id": "7dc6cb40b962515d",
        "type": "link in",
        "z": "d25643a6d08051c3",
        "name": "",
        "links": [
            "8b3fc2161e7352a9"
        ],
        "x": 1935,
        "y": 140,
        "wires": [
            [
                "a8e450bf650164b8"
            ]
        ]
    },
    {
        "id": "a075be3dde31eb3c",
        "type": "link in",
        "z": "d25643a6d08051c3",
        "name": "",
        "links": [
            "8b3fc2161e7352a9"
        ],
        "x": 1395,
        "y": 140,
        "wires": [
            [
                "cc60f48987306122"
            ]
        ]
    },
    {
        "id": "cc60f48987306122",
        "type": "delay",
        "z": "d25643a6d08051c3",
        "name": "",
        "pauseType": "delay",
        "timeout": "200",
        "timeoutUnits": "milliseconds",
        "rate": "1",
        "nbRateUnits": "1",
        "rateUnits": "second",
        "randomFirst": "1",
        "randomLast": "5",
        "randomUnits": "seconds",
        "drop": false,
        "allowrate": false,
        "outputs": 1,
        "x": 1520,
        "y": 140,
        "wires": [
            [
                "b0783ecb7cd0b347"
            ]
        ]
    },
    {
        "id": "a8e450bf650164b8",
        "type": "delay",
        "z": "d25643a6d08051c3",
        "name": "",
        "pauseType": "delay",
        "timeout": "200",
        "timeoutUnits": "milliseconds",
        "rate": "1",
        "nbRateUnits": "1",
        "rateUnits": "second",
        "randomFirst": "1",
        "randomLast": "5",
        "randomUnits": "seconds",
        "drop": false,
        "allowrate": false,
        "outputs": 1,
        "x": 2060,
        "y": 140,
        "wires": [
            [
                "01b247835b6902e3"
            ]
        ]
    },
    {
        "id": "22da5afa7a176845",
        "type": "delay",
        "z": "d25643a6d08051c3",
        "name": "",
        "pauseType": "delay",
        "timeout": "200",
        "timeoutUnits": "milliseconds",
        "rate": "1",
        "nbRateUnits": "1",
        "rateUnits": "second",
        "randomFirst": "1",
        "randomLast": "5",
        "randomUnits": "seconds",
        "drop": false,
        "allowrate": false,
        "outputs": 1,
        "x": 2740,
        "y": 140,
        "wires": [
            [
                "4ea800fc81f0914b"
            ]
        ]
    },
    {
        "id": "4709d963cca3a76f",
        "type": "server-state-changed",
        "z": "d25643a6d08051c3",
        "name": "fan turned on",
        "server": "d780e50948c91305",
        "version": 4,
        "exposeToHomeAssistant": false,
        "haConfig": [
            {
                "property": "name",
                "value": ""
            },
            {
                "property": "icon",
                "value": ""
            }
        ],
        "entityidfilter": "fan.purifier_fan",
        "entityidfiltertype": "exact",
        "outputinitially": false,
        "state_type": "str",
        "haltifstate": "on",
        "halt_if_type": "str",
        "halt_if_compare": "is",
        "outputs": 2,
        "output_only_on_state_change": true,
        "for": "0",
        "forType": "num",
        "forUnits": "minutes",
        "ignorePrevStateNull": false,
        "ignorePrevStateUnknown": false,
        "ignorePrevStateUnavailable": false,
        "ignoreCurrentStateUnknown": false,
        "ignoreCurrentStateUnavailable": false,
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "entityState"
            },
            {
                "property": "data",
                "propertyType": "msg",
                "value": "",
                "valueType": "eventData"
            },
            {
                "property": "topic",
                "propertyType": "msg",
                "value": "",
                "valueType": "triggerId"
            }
        ],
        "x": 150,
        "y": 800,
        "wires": [
            [
                "29cba535f7c116c2"
            ],
            []
        ]
    },
    {
        "id": "06b73c058422a3e6",
        "type": "api-current-state",
        "z": "d25643a6d08051c3",
        "name": "Favorite Mode?",
        "server": "d780e50948c91305",
        "version": 3,
        "outputs": 2,
        "halt_if": "Favorite",
        "halt_if_type": "str",
        "halt_if_compare": "is",
        "entity_id": "sensor.nodered_fan_preset_mode",
        "state_type": "str",
        "blockInputOverrides": false,
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "entityState"
            },
            {
                "property": "data",
                "propertyType": "msg",
                "value": "",
                "valueType": "entity"
            }
        ],
        "for": "0",
        "forType": "num",
        "forUnits": "minutes",
        "override_topic": false,
        "state_location": "payload",
        "override_payload": "msg",
        "entity_location": "data",
        "override_data": "msg",
        "x": 180,
        "y": 860,
        "wires": [
            [
                "bd570d4fcc0d64ce"
            ],
            []
        ]
    },
    {
        "id": "bd570d4fcc0d64ce",
        "type": "api-current-state",
        "z": "d25643a6d08051c3",
        "name": "read Favorite level",
        "server": "d780e50948c91305",
        "version": 3,
        "outputs": 1,
        "halt_if": "",
        "halt_if_type": "str",
        "halt_if_compare": "is",
        "entity_id": "input_number.fan_favorite_level",
        "state_type": "str",
        "blockInputOverrides": false,
        "outputProperties": [
            {
                "property": "payload",
                "propertyType": "msg",
                "value": "",
                "valueType": "entityState"
            },
            {
                "property": "data",
                "propertyType": "msg",
                "value": "",
                "valueType": "entity"
            }
        ],
        "for": "0",
        "forType": "num",
        "forUnits": "minutes",
        "override_topic": false,
        "state_location": "payload",
        "override_payload": "msg",
        "entity_location": "data",
        "override_data": "msg",
        "x": 390,
        "y": 860,
        "wires": [
            [
                "87dd717b970f7079"
            ]
        ]
    },
    {
        "id": "29cba535f7c116c2",
        "type": "delay",
        "z": "d25643a6d08051c3",
        "name": "",
        "pauseType": "delay",
        "timeout": "1",
        "timeoutUnits": "seconds",
        "rate": "1",
        "nbRateUnits": "1",
        "rateUnits": "second",
        "randomFirst": "1",
        "randomLast": "5",
        "randomUnits": "seconds",
        "drop": false,
        "allowrate": false,
        "outputs": 1,
        "x": 380,
        "y": 820,
        "wires": [
            [
                "06b73c058422a3e6"
            ]
        ]
    },
    {
        "id": "d780e50948c91305",
        "type": "server",
        "name": "Home Assistant",
        "version": 2,
        "addon": true,
        "rejectUnauthorizedCerts": true,
        "ha_boolean": "y|yes|true|on|home|open",
        "connectionDelay": true,
        "cacheJson": true,
        "heartbeat": false,
        "heartbeatInterval": "30"
    }
]
```

</details></p>

<br>

## HA Helpers

These helpers are necessary to change the fan.

### Favorite level

&nbsp; &nbsp; Name in the flow: input_number.fan_favorite_level <br>
&nbsp; &nbsp; icon: mdi:fan-plus <br>
&nbsp; &nbsp; Min-Value: 0 <br>
&nbsp; &nbsp; Max-Value: 14 <br>

## Switch Child lock

&nbsp; &nbsp; Name in the flow: input_boolean.fan_child_lock <br>
&nbsp; &nbsp; mdi:lock-open-outline <br>

## Switch LED

&nbsp; &nbsp; input_boolean.2s_led <br>
&nbsp; &nbsp; mdi:lightbulb-auto <br>