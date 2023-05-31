# Home Assistant Jinja Templating to anmnounce Wind Details

## get the current wind speed and wind bearing in degrees from Home Assistant
```jinja
{% set wind_bearing = state_attr('weather.home', 'wind_bearing') | float %}
{% set wind_speed = state_attr('weather.home','wind_speed') | float %}
```

## This array is a list of Cardinal Directions allowing us to convert the bearing into one of them. 
### Admittedly, This is more complicated than it needs to be. 
```jinja
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
```

## Announce a qualitative description of wind speed
```jinja
{% if wind_speed > 50 %}
  Its very windy outside.
{% elif 40 < wind_speed < 50 %}
  It's pretty windy at the moment
{% elif 30 < wind_speed < 40 %}
  Theres a strong breeze right now
{% elif 20 < wind_speed < 30 %}
  It's a bit breezy outside
{% elif 10 < wind_speed < 20 %}
  There's a light breeze
{% else %}
  it's quite still
{% endif %}
```


### Announce the Cardinal Direction and Wind Speed
```jinja
{% for rng, direction in directions.items() %}
  {% if wind_bearing >= rng[0] and wind_bearing < rng[1] %}
    The wind is currently blowing from the {{ direction }} direction, at {{ wind_speed }} kilometres per hour
  {% endif %}
{% endfor %}
```