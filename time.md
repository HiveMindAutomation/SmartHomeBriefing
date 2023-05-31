# Jinja template for Home Assistant for Time

## get Current Time from Home Assistant
```jinja
{% set current_time = states('sensor.time') %}
```
### extract the current hour
```jinja
{% set hour = current_time.split(':')[0] | int %}
```
### and Minute
```jinja
{% set minute = current_time.split(':')[1] %}
```

## Say a time appropriate greeting
```jinja
{% if hour >= 6 and hour < 12 %}
    Good morning!
{% elif hour >= 12 and hour < 18 %}
    Good afternoon!
{% elif hour >= 18 or hour < 6 %}
    Good evening!
{% endif %}
```

## Add AM or PM Suffix for announcement
```jinja
{% set suffix = 'AM' if hour < 12 else 'PM' %}
```
## convert 24 hour time to 12 hour time
```jinja
{% set hour_12 = hour if hour <= 12 else hour - 12 %}
```
## Announce the current Time
```jinja
It's currently {{ hour_12 }}:{{ minute }} {{ suffix }}.
```

## Put it all together:
```jinja
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
```

## The Home Assistant Script
```yaml
announce_time:
  alias: Announce Time
  sequence:
  - service: notify.alexa_media
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

        {% set suffix = ''AM'' if hour < 12 else 'PM' %} 
        {% set hour_12 = hour if hour <= 12 else hour - 12 %}

        It's currently {{ hour_12 }}:{{ minute }} {{ suffix }}.
        "
      target:
      - media_player.dining_room_echo_plus
  mode: single
```