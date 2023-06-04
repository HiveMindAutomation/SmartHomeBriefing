# Home Assistant Jinja 2 Templates for Weather Temperatures

[Back to README](./README.md)  


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

The weather entity stores forecast data in the forecast attribute as an array of forecasts.
The forecast array looks a little like this.
Now it's only in this writeup that I've made the realisation that I got some fundamental concepts in my video wrong.
Each item in the "forecast" array is actually for a "Day" not an hour..... OOPS!.

```yaml
forecast: 
- condition: partlycloudy
  datetime: '2023-06-05T02:00:00+00:00'
  wind_bearing: 118.2
  temperature: 17.7
  templow: 8.5
  wind_speed: 18.4
  precipitation: 2.7
- condition: partlycloudy
  datetime: '2023-06-06T02:00:00+00:00'
  wind_bearing: 14.2
  temperature: 17.8
  templow: 11.8
  wind_speed: 21.2
  precipitation: 0.1
- condition: rainy
  datetime: '2023-06-07T02:00:00+00:00'
  wind_bearing: 20.6
  temperature: 17.3
  templow: 12.7
  wind_speed: 18.7
  precipitation: 23.9
- condition: partlycloudy
  datetime: '2023-06-08T02:00:00+00:00'
  wind_bearing: 277.1
  temperature: 14.6
  templow: 10.3
  wind_speed: 15.8
  precipitation: 0.6
- condition: partlycloudy
  datetime: '2023-06-09T02:00:00+00:00'
  wind_bearing: 281.9
  temperature: 13.8
  templow: 10.4
  wind_speed: 20.2
  precipitation: 0.4
```

```jinja
{% set high_temp = weather[0].temperature %}
{% set low_temp = weather[0].templow %}
```

### Some Qualitative statements about the `high_temp`
This code block looks at the `high_temp` variable and runs the if statement below to make a qualitative statement about it.
These have been built for °C and I pulled the qualitative statements out of my head.
Change them as you need to

- `Above 30°C` say "It's going to be a hot one tomorrow"
- `between 20°C and 30°C` say "It should be a warm day tomorrow"
- `between 15°C and 20°C` say "It's going to be a pretty mild temperature tomorrow"
- `between 10°C and 15°C` say "It seems like it'll be a bit chilly tomorrow"
- `between 0°C and 10°C` say "It's going to be a cold day tomorrow"
- `< 0°C` say "It's going to be freezing tomorrow"

```jinja
{% if high_temp > 30 %}
  It's going to be a hot one tomorrow
{% elif 20 < high_temp < 30 %}
  It should be a warm day tomorrow
{% elif 15 < high_temp < 20 %}
  It's going to be a pretty mild temperature tomorrow
{% elif 10 < high_temp < 15 %}
  It seems like it'll be a bit chilly tomorrow
{% elif 0 < high_temp < 10 %}
  It's going to be a cold day tomorrow
{% else %}
  It's going to be freezing tomorrow
{% endif %}
```

### Announce the `high_temp` and `low_temp` values
Again, built for °C

```jinja
The forecast high for tomorrow is {{ high_temp }} °C.
{{ forecast_condition }} With a Low of {{ low_temp }} °C .
```

### Some qualitative statements about the `low_temp`
Again we're looking at °C and the statements and ranges are arbitrary, and can be adjusted to your tastes

Remember these are for the `low_temp` variable, so it's not going to get colder than these  

- `Above 30°C` say "Wow. That's hot!"
- `between 20°C and 30°C` say "it's not going to get below 20°C tomorrow"
- `between 10°C and 20°C` say "it might get a little bit chilly tomorrow."
- `between 0°C and 10°C` say "That's cold."
- `< 0°C` say "Absolutely freezing!"

Puntuation like `.`, `!`, `?` and `,` is important in the text to speech statments so the timing sounds "natural"

```jinja
{% if low_temp > 30 %}
  Wow. That's hot!
{% elif 20 < low_temp < 30 %}
  it's not going to go below 20°C tomorrow.
{% elif 10 < low_temp < 20 %}
  it might get a little bit chilly tomorrow.
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

{% set forecast_condition = weather[0].condition %}

{% if high_temp > 30 %}
  It's going to be a hot one tomorrow
{% elif 20 < high_temp < 30 %}
  It should be a warm day tomorrow
{% elif 15 < high_temp < 20 %}
  It's going to be a pretty mild temperature tomorrow
{% elif 10 < high_temp < 15 %}
  It seems like it'll be a bit chilly tomorrow
{% elif 0 < high_temp < 10 %}
  It's going to be a cold day tomorrow
{% else %}
  It's going to be freezing tomorrow
{% endif %}

The forecast high for tomorrow is {{ high_temp }} °C.
{{ forecast_condition }} With a Low of {{ low_temp }} °C .


{% if low_temp > 30 %}
  Wow. That's hot!
{% elif 20 < low_temp < 30 %}
  Pretty warm
{% elif 10 < low_temp < 20 %}
  it might get a little bit chilly.
{% elif 0 < low_temp < 10 %}
  That's cold.
{% else %}
  Absolutely freezing!
{% endif %}
```

## Put it in a Script
```yaml
alias: Announce Weather Details
  sequence:
  - service: notify.alexa_media
    data:
      message: >_
      {% set current_temp = state_attr ('weather.home','temperature') | float %}
      {% set current_condition = states('weather.home') %}

      {% if current_condition == 'partlycloudy' %}
        {% set current_condition = 'Partly Cloudy' %}
      {% endif %}

      Outside, It's {{ current_condition }} and {{ current_temp }} degrees Celsius.

      {% set weather = state_attr('weather.home', 'forecast') %}
      {% set high_temp = weather[0].temperature %}
      {% set low_temp = weather[0].templow %}

      {% set forecast_condition = weather[0].condition %}

      {% if high_temp > 30 %}
        It's going to be a hot one tomorrow
      {% elif 20 < high_temp < 30 %}
        It should be a warm day tomorrow
      {% elif 15 < high_temp < 20 %}
        It's going to be a pretty mild temperature tomorrow
      {% elif 10 < high_temp < 15 %}
        It seems like it'll be a bit chilly tomorrow
      {% elif 0 < high_temp < 10 %}
        It's going to be a cold day tomorrow
      {% else %}
        It's going to be freezing tomorrow
      {% endif %}

      The forecast high for tomorrow is {{ high_temp }} °C.
      {{ forecast_condition }} With a Low of {{ low_temp }} °C .


      {% if low_temp > 30 %}
        Wow. That's hot!
      {% elif 20 < low_temp < 30 %}
        Pretty warm
      {% elif 10 < low_temp < 20 %}
        it might get a little bit chilly.
      {% elif 0 < low_temp < 10 %}
        That's cold.
      {% else %}
        Absolutely freezing!
      {% endif %}

    - media_player.dining_room_echo_plus
  mode: single
```