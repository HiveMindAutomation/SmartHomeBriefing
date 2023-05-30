# Jinja template for Home Assistant for Time

## get Current Time from Home Assistant
```
{% set current_time = states('sensor.time') %}
```
### extract the current hour
```
{% set hour = current_time.split(':')[0] | int %}
```
### and Minute
```
{% set minute = current_time.split(':')[1] %}
```

## Say a time appropriate greeting
```
{% if hour >= 6 and hour < 12 %}
    Good morning!
{% elif hour >= 12 and hour < 18 %}
    Good afternoon!
{% elif hour >= 18 or hour < 6 %}
    Good evening!
{% endif %}
```

## Add AM or PM Suffix for announcement
```
{% set suffix = 'AM' if hour < 12 else 'PM' %}
```
## convert 24 hour time to 12 hour time
```
{% set hour_12 = hour if hour <= 12 else hour - 12 %}
```
## Announce the current Time
It's currently {{ hour_12 }}:{{ minute }} {{ suffix }}.

## Put it all together:
```
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