# Hive Mind Automation - Video - Getting a Briefing from your Smart Home in Home Assistant

## Preface

This serves as supplemental information for the Hive Mind Automation YouTube Video  
[Create a Daily Briefing with Home Assistant](https://youtu.be/A_m-ik4fRzo)

It contains code snippets of Jinja2 Templates used in the announcements, and I've added a `scripts.yaml` file so you can see how I use it in my setup. 

I've broken the scripts into individual snippets to be triggered by the automation.

Reference materials are available at:


[Time Announcement](./time.md)  
[Weather - Temperature](./weatherTemperature.md)  
[Weather - Wind](./weatherWind.md)  
[Inside Temperatures](./inside%20temps.md)  
[Dad Joke, Quote and Random Fact](./fun.md)  
[Pending Updates](./pending%20updates.md)  

## Script Contents
The Basis of each Script in Home Assistant is:
```
service: notify.alexa_media
data:
  data:
    type: tts
  message: "
    <YOUR TEMPLATED MESSAGE HERE>
  "
  target:
    - media_player.dining_room_echo_plus
    - media_player.bedroom_echo_dot
    - media_player.office_echo_dot
    - media_player.spare_room_echo_dot
```
Where we'll then swap out `<YOUR TEMPLATED MESSAGE HERE>` for what we want to actually say.
In this case, I'm using the Alexa Media ACS add-on, but you could reasonably use this with Google home, or ANY text to speech engine from Home Assistant, including 

## TO-DO's

- Turn multi-value outputs into an array of dictionaries/tuples and run through the template in a for loop.
    - i.e. Indoor Temperatures & pending updates. 
- 
