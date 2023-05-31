# Home Assistant Jinja 2 Templating to announce some inside Temperatures


This process is fairly simple. We don't need to iterate over arrays or anything like that unless we want to get complicated.

For my use case and for the YouTube Video, I chose to read out the temperatures in 4 rooms:
This table shows the rooms and the sensor ID's I chose, your smart home will differ.
|  |  |  |
|--|--|--|
|Dining Room| sensor.temperature_dining| Aqara Temperature and Humidity Sensor
|Lounge Room| sensor.lounge_ac_inside_temperature| Temperature Sensor built into my Loungeroom Air Conditioner
|Office | sensor.temperature_office| Aqara Temperature and Humidity Sensor
|Master Bedroom| sensor.master_bedroom_purifier_temperature| Temp and Humidity Sensor built into my XiaoMi Air Purifier

You should choose the data points that matter the most to you.
You can add humidity data if you want

### The code to announce the states.
```jinja
Inside it's currently {{ states('sensor.temperature_dining') }} °C in the Dining Room, 
{{ states('sensor.lounge_ac_inside_temperature') }} °C in the Lounge, 
{{ states('sensor.temperature_office')}} °C in the office,
and {{ states('sensor.master_bedroom_purifier_temperature') }} °C in the Master Bedroom.
```