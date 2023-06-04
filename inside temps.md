# Home Assistant Jinja 2 Templating to announce some inside Temperatures

[Back to README](./README.md)  


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

### But there's a better way!
We're going to crate an Array of Dictionaries, and iterate through the array.  

```jinja

{% set inside_temps = {
    # A List of Dictionaries
    # The Dictionary Key is the `entity_id` of the temperature sensor in Home Assistant
    # the Dictionary Value is the Name of the room we want our TTS engine to say.
    # For the TTS to sound more 'natural' I've added ", and" to the second last item in the array
    
    'sensor.temperature_dining': 'Dining Room',
    'sensor.lounge_ac_inside_temperature': 'Lounge Room',
    'sensor.temperature_office': 'Office, and',
    'sensor.master_bedroom_purifier_temperature': 'Master Bedroom'

} %}



Inside it's currently 
{% for sensor, room in inside_temps.items() %}
    {{ states(sensor) }} °C in the {{ room }}
{% endfor %}
```