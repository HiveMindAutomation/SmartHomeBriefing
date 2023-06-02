__Service Call__:

[Back to README](./README.md)  

```yaml
service: notify.alexa_media_office_echo_dot
data:
  message: "
  
{% set current_temp = state_attr ('weather.home','temperature') | float %}
{% set wind_bearing = state_attr('weather.home', 'wind_bearing') | float %}
{% set wind_speed = state_attr('weather.home','wind_speed') | float %}
{% set current_condition = states('weather.home') %}


{% if current_condition == 'partlycloudy' %}
  {% set current_condition = 'Partly Cloudy' %}
{% endif %}

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

# get Current Time from Home Assistant
{% set current_time = states('sensor.time') %}
# Split time into hour
{% set hour = current_time.split(':')[0] | int %}
# and Minute
{% set minute = current_time.split(':')[1] %}

# Say a time appropriate greeting
{% if hour >= 6 and hour < 12 %}
    Good morning!
{% elif hour >= 12 and hour < 18 %}
    Good afternoon!
{% elif hour >= 18 or hour < 6 %}
    Good evening!
{% endif %}

# Add AM or PM Suffix
{% set suffix = 'AM' if hour < 12 else 'PM' %}
{% set hour_12 = hour if hour <= 12 else hour - 12 %}

# Announce the current Time
It's currently {{ hour_12 }}:{{ minute }} {{ suffix }}.

Outside, It's {{ current_condition }} and {{ current_temp }} degrees Celsius.

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

{% if high_temp > 30 %}
  It's going to be a hot one
{% elif 20 < high_temp < 30 %}
  It should be a warm day
{% elif 15 < high_temp < 20 %}
  It's going to be a pretty mild temperature today
{% elif 10 < high_temp < 15 %}
  It seems like it'll be a bit chilly today
{% elif 0 < high_temp < 10 %}
  It's going to be a cold day
{% else %}
  It's going to be freezing today
{% endif %}

The forecast high is {{ high_temp }} °C.

With a Low of {{ low_temp }} °C .
{% if low_temp > 30 %}
  Wow. That's hot!
{% elif 20 < low_temp < 30 %}
  it's not going to go below 20 degrees today.
{% elif 10 < low_temp < 20 %}
  it might get a little bit chilly.
{% elif 0 < low_temp < 10 %}
  That's cold.
{% else %}
  Absolutely freezing!
{% endif %}

{% if wind_speed > 50 %}
  Its very windy outside.
{% endif %}
{% for rng, direction in directions.items() %}
  {% if wind_bearing >= rng[0] and wind_bearing < rng[1] %}
    The wind is currently blowing from the {{ direction }} direction, at {{ wind_speed }} kilometres per hour
  {% endif %}

{% endfor %}

Inside it's currently {{ states('sensor.temperature_dining') }} °C in the Dining Room, 
{{ states('sensor.lounge_ac_inside_temperature') }} °C in the Lounge, 
{{ states('sensor.temperature_office')}} °C in the office,
and {{ states('sensor.master_bedroom_purifier_temperature') }} °C in the Master Bedroom.

The {{ state_attr( 'sensor.quote_sensor', 'category' ) }} Quote of the day is by {{ state_attr( 'sensor.quote_sensor', 'author' ) }}. 
They said. {{ states('sensor.quote_sensor') }}

Here is a Dad Joke.. {{ states('sensor.dad_joke_of_the_hour') }}

And a random fact.. {{ states('sensor.random_fact') }}

{% if states('update.home_assistant_operating_system_update') == 'off' %}
There's a Home Assistant O S Update pending. 
The Installed version is {{ state_attr('update.home_assistant_operating_system_update', 'installed_version') }} 
The Available version is {{ state_attr('update.home_assistant_operating_system_update', 'latest_version') }} 
{% endif %}

{% if states('update.home_assistant_core_update') == 'off' %}
There's a Home Assistant Core Update pending. 
The Installed version is {{ state_attr('update.home_assistant_core_update', 'installed_version') }} 
The Available version is {{ state_attr('update.home_assistant_core_update', 'latest_version') }} 
{% endif %}

{% if states('update.esphome_update') == 'off' %} 
There's an E S P Home Update pending. 
The Installed version is {{ state_attr('update.esphome_update', 'installed_version') }} 
The Available version is {{ state_attr('update.esphome_update', 'latest_version') }} 
{% endif %}

```