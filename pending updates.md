# Home Assistant Pending Updates Announcement

I also wanted The Briefing to announce if there's any pending Home Assistant updates to be installed, but I want to limit it down to only the Home Assistant Core, Home Assistant OS, and ESPHome.

![Home Assistant OS Pending Update](pendingUpdate.png)

So I came up with the following Template

In hindsight, I should probably put this into a for loop.
I'll do that for version 2 maybe....

### The Template
```jinja
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

### The Home Assistant Script
Here's the script I put into my `scripts.yaml` or you can just paste it into the Home Assistant UI when editing the script in YAML mode.
```yaml
alias: Announce Pending Updates
sequence:
  - service: notify.alexa_media
    data:
      message: >-
        {% if states('update.home_assistant_operating_system_update') == 'on' %}
        There's a Home Assistant O S Update pending.  The Installed version is
        {{ state_attr('update.home_assistant_operating_system_update',
        'installed_version') }}.  
        The Available version is {{ state_attr('update.home_assistant_operating_system_update', 'latest_version') }}.  
        {% endif %} 
        {% if states('update.home_assistant_core_update') == 'on' %} 
        There's a Home Assistant Core Update pending.  The Installed version is 
        {{ state_attr('update.home_assistant_core_update', 'installed_version') }}.  
        The Available version is {{ state_attr('update.home_assistant_core_update', 'latest_version') }}. 
        {% endif %}

        {% if states('update.esphome_update') == 'on' %}  
        There's an E S P Home Update pending.  The Installed version is 
        {{ state_attr('update.esphome_update', 'installed_version') }}.  
        The Available version is 
        {{ state_attr('update.esphome_update', 'latest_version') }}.  
        {% endif %} 
      target:
        - media_player.dining_room_echo_plus
mode: single
icon: mdi:update
```
