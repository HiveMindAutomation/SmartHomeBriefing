# Notes for creating a morning briefing from my smart home.

## Determining the data points that we want

__Current Temperature:__ `{{ state_attr ('weather.home','temperature') }}`  
__Temperature Unit:__ `{{ state_attr ('weather.home','temperature_unit') }}`  
__Wind-Speed:__ `{{ state_attr ('weather.home','wind_speed') }}`  
__Wind-Speed-Unit:__ `{{ state_attr ('weather.home','wind_speed_unit') }}`  
__Wind-Bearing:__ `{{ state_attr ('weather.home','temperature') }}`  

__Current State:__ `{{ states('weather.home') }}`
  
__Wind-Cardinal:__ if Statement to determine Cardinal Direction of wind

```
{% set wind_bearing = state_attr('weather.home', 'wind_bearing') | float %}

{% set directions = {
    (0.0, 11.25): 'North',
    (11.25, 33.75): 'North North East',
    (33.75, 56.25): 'North East',
    (56.25, 78.75): 'East North East',
    (78.75, 101.25): 'East',
    (101.25, 123.75): 'East South East',
    (123.75, 146.25): 'South Eeast',
    (146.25, 168.75): 'South South East',
    (168.75, 191.25): 'South',
    (191.25, 213.75): 'South South West',
    (213.75, 236.25): 'South West',
    (236.25, 258.75): 'West South West',
    (258.75, 281.25): 'West',
    (281.25, 303.75): 'West North West',
    (303.75, 326.25): 'North West',
    (326.25, 348.75): 'North Nort West',
    (348.75, 360.0): 'North'
} %}

{% for rng, direction in directions.items() %}
  {% if wind_bearing >= rng[0] and wind_bearing < rng[1] %}
    The wind is blowing from the {{ direction }} direction.
  {% endif %}
{% endfor %}
```

__Forecast Conditions__:

```

{% for forecast_item in state_attr('weather.home', 'forecast' ) %}
  {{ forecast_item.datetime }} 
  {% if forecast_item.condition == 'rainy'%}    
    It looks like there's rain expected at around {{ forecast_item.datetime.split('T')[1][:2] }} o'clock. You might want to take an umbrella if you're leaving the house.
  {% else %}
  {% endif %}
{% endfor %}
```
__Most Common Condition:__
```
{% set conditions = [] %}
{% for item in forecast %}
    {% set _ = conditions.append(item.condition) %}
{% endfor %}

{% set most_common_condition = conditions|groupby('self')|map('list')|max(attribute='|length')|first %}

Most common condition: {{ most_common_condition }}


```
__Forecast High/Low Temp__
```
{% set weather = state_attr('weather.home', 'forecast') %}
{% set high_temp = weather[0].temperature %}
{% set low_temp = weather[0].templow %}

{% for entry in weather %}
  {% if entry.temperature > high_temp %}
    {% set high_temp = entry.temperature %}
  {% endif %}
  {% if entry.templow < low_temp %}
    {% set low_temp = entry.templow %}
  {% endif %}
  {% if entry.condition == 'rainy'%}    
    It looks like there's rain expected at around {{ entry.datetime.split('T')[1][:2] }} o'clock. You might want to take an umbrella if you're leaving the house.
  {% endif %}
{% endfor %}

The forecast high is {{ high_temp }} °C
With a Low of {{ low_temp }} °C
```

__Abstract it all:__

Break it all into Scripts and trigger them in sequence with the Automation.



```
service: notify.alexa_media_office_echo_dot
data:
  message: "

{% set current_time = states('sensor.time') %}
{% set hour = current_time.split(':')[0] | int %}
{% set minute = current_time.split(':')[1] %}

{% if hour >= 6 and hour < 12 %}
    Good morning!
{% elif hour >= 12 and hour < 18 %}
    Good afternoon!
{% elif hour >= 18 or hour < 6 %}
    Good evening!
{% endif %}

{% set suffix = 'AM' if hour < 12 else 'PM' %}
{% set hour_12 = hour if hour <= 12 else hour - 12 %}

It's currently {{ hour_12 }}:{{ minute }} {{ suffix }}.
"
```