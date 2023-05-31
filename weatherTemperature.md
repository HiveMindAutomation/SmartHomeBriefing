# Home Assistant Jinja 2 Templates for Weather Temperatures

## Step-by-step

### Get the current Temperature from Home Assistant
We're casting this as a float, probably no need, but I'd rather be safe.
```jinja
{% set current_temp = state_attr ('weather.home','temperature') | float %}
```

### Get the current weather condition from Home Assistant as well
```jinja
{% set current_condition = states('weather.home') %}
```
### Fix the current Condition
When the Current weather condition is "Partly Cloudy" the default weather integration returns `partlycloudy` and it sounds odd when read out by a Text to Speech engine.  
To fix it I added:
```jinja
{% if current_condition == 'partlycloudy' %}
  {% set current_condition = 'Partly Cloudy' %}
{% endif %}
```

## Announce the current condition and Temperature 
```jinja
Outside, It's {{ current_condition }} and {{ current_temp }} degrees Celsius.
```

## Figure out the Highs and Lows from the forecast
For weather information, I want to know the High and Low temp. This data is strangely not included by default in the default weather integration, so we have to figure it out ourselves.

### Start by Extracting the `forecast` array data into a variable
```jinja
{% set weather = state_attr('weather.home', 'forecast') %}
```

### Instantiate a `high_temp` and `low_temp` variable
We're assigning the temperature from the 0th record in the array to get started.

```jinja
{% set high_temp = weather[0].temperature %}
{% set low_temp = weather[0].templow %}
```

### Iterate through the array and set the `high_temp` or `low_temp` variables accordingly.
I've also added an `if` statement in here to announce if any of the forecast conditions are `rainy` and the `hour` that the rain is forecast for and to tell us to take an umbrella.
I've surrounded that code block with `###########` for clarity

```jinja
{% for entry in weather %}
  {% if entry.temperature > high_temp %}
    {% set high_temp = entry.temperature %}
  {% endif %}
  {% if entry.templow < low_temp %}
    {% set low_temp = entry.templow %}
  {% endif %}

  ###########
  # While we're here, if the condition for this hour is `rainy` let's announce that
  {% if entry.condition == 'rainy'%}    
    It looks like there's rain expected at around {{ entry.datetime.split('T')[1][:2] }} o'clock. 
    You might want to take an umbrella if you're leaving the house.
  {% endif %}
  ###########

{% endfor %}
```

### Some Qualitative statements about the `high_temp`
This code block looks at the `high_temp` variable and runs the if statement below to make a qualitative statement about it.
These have been built for °C and I pulled the qualitative statements out of my head.
Change them as you need to

`Above 30°C` say "It's going to be a hot one"  
`between 20°C and 30°C` say "It should be a warm day"  
`between 15°C and 20°C` say "It's going to be a pretty mild temperature today"  
`between 10°C and 15°C` say "It seems like it'll be a bit chilly today"  
`between 0°C and 10°C` say "It's going to be a cold day"  
else: the `high_temp` variable must be < 0°C  
`< 0°C` say "It's going to be freezing today"

```jinja
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
```

### Announce the `high_temp` and `low_temp` values
Again, built for °C

```jinja
The forecast high is {{ high_temp }} °C.
With a Low of {{ low_temp }} °C .
```

### Some qualitative statements about the `low_temp`
Again we're looking at °C and the statements and ranges are arbitrary, and can be adjusted to your tastes

Remember these are for the `low_temp` variable, so it's not going to get colder than these  

`Above 30°C` say "Wow. That's hot!"  
`between 20°C and 30°C` say "it's not going to get below 20°C toda.y"  
`between 10°C and 20°C` say "it might get a little bit chilly."  
`between 0°C and 10°C` say "That's cold."  
`< 0°C` say "Absolutely freezing!"

Puntuation like `.`, `!`, `?` and `,` is important in the text to speech statments so the timing sounds "natural"

```jinja
{% if low_temp > 30 %}
  Wow. That's hot!
{% elif 20 < low_temp < 30 %}
  it's not going to go below 20°C today.
{% elif 10 < low_temp < 20 %}
  it might get a little bit chilly.
{% elif 0 < low_temp < 10 %}
  That's cold.
{% else %}
  Absolutely freezing!
{% endif %}
```

## Now we can put it all together:
```jinja
{% set current_temp = state_attr ('weather.home','temperature') | float %}
{% set current_condition = states('weather.home') %}

{% if current_condition == 'partlycloudy' %}
  {% set current_condition = 'Partly Cloudy' %}
{% endif %}

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
```