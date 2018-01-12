# hass-artnet
Home Assistant DMX Light Platform (Art-Net)

The Art-Net integration for Home Assistant allows you to send DMX values to an [Art-Net](http://www.art-net.org.uk) capable DMX interface.

The component is a one way integration, and sends UDP packets to the Art-Net interface. This integration uses no external libraries and requires at least Python version 3.5.

### Usage

To use DMX in your installation:
1. Download the [artnet.py](https://github.com/jnimmo/hass-artnet/raw/master/artnet.py) file and save into the *'custom_components/light'* directory. (Create a *'custom_components'* folder in the location of your configuration.yaml file, and create a subdirectory *'light'* to store this platform)
2. Add the following lines to your `configuration.yaml` file:

```yaml
light:
  - platform: artnet
    host: <IP Address>
    port: 6454
    dmx_channels: 512 
    default_level: 0
    devices:
      - channel: 1
        name: House lights
        type: dimmer
        transition: 3
      - channel: 2
        name: Hall lights
        type: dimmer
        default_level: 255
      - channel: 3
        name: Stair lights
        type: dimmer
        transition: 3
      - channel: 4
        type: rgb
        name: Entrance LED Strip
        default_rgb: [0,0,150]
      - channel: 7
        type: dimmer
        name: Smoke machine
```

Configuration variables:
- **host** (*Required*): Host Art-Net/DMX gateway
- **port** (*Optional*): Defaults to 6454
- **dmx_channels** (*Required*): The number of DMX channels to send a value for (even number between 2 & 512)
- **default_level** (*Required*): Default level to assume the lights have been set to - in most cases 0 would make sense. Note Home Assistant will not send these values to the gateway until an explicit change is made.

Device configuration variables:
- **channel** (*Required*): The base DMX channel for the light (1-512)
- **name** (*Required*): Friendly name for the light (will also be used for the entity_id)
- **type** (*Required*): 'dimmer' (single channel), 'rgb' (three channel rgb), 'rgbw' (three channel rgb + white), 'rgbw_auto' (three channel rgb + automatically calculated white value) or 'switch' (single channel with no brightness adjustments)
- **default_level** (*Optional*): Default level to give to Home Assistant for the light (0-255)
- **default_rgb** (*Optional*): Default colour to give to Home Assistant for the light in the format [R,G,B]
- **white_level** (*Optional*): Default white level for RGBW lights (0-255)
- **transition** (*Optional*): Set a default fade time for transitions. Transition times specified through the turn_on / turn_off service calls in Home Assistant will override this behaviour. 

Supported features:
- Transition time can be specified through services to fade to a colour (for RGB fixtures) or value. This currently is set to run at 40 frames per second. Multiple fades at the same time seem to be possible.
- Brightness: Once a channel is turned on brightness can be controlled through the Home Assistant interface.
- White level: For RGB lights with a separate white LED this controls the white LED. This can be automatically controlled using the colour wheel on 'rgbw_auto' lights, or manually with 'rgbw'

Limitations:
- Currently hard-coded to only address a single DMX universe
- DMX frames must send values for all channels in a universe. If you have other channels which are controlled by a different device or lighting desk, set Home Assistant to default to 0 values; and set your Art-Net device to merge on highest value rather than most recent update. This means channels could be controlled from either the desk or Home Assistant.


**Art-Net™ Designed by and Copyright Artistic Licence Holdings Ltd**
