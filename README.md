[![](https://img.shields.io/github/release/jaruba/ha-samsungtv-tizen/all.svg?style=for-the-badge)](https://github.com/jaruba/ha-samsungtv-tizen/releases)
[![hacs_badge](https://img.shields.io/badge/HACS-Default-orange.svg?style=for-the-badge)](https://github.com/custom-components/hacs)
[![](https://img.shields.io/github/license/jaruba/ha-samsungtv-tizen?style=for-the-badge)](LICENSE)
[![](https://img.shields.io/badge/MAINTAINER-%40jaruba-red?style=for-the-badge)](https://github.com/jaruba)
[![](https://img.shields.io/badge/COMMUNITY-FORUM-success?style=for-the-badge)](https://community.home-assistant.io)

# HomeAssistant - by jayanth

This is a custom component to allow control of SamsungTV devices in [HomeAssistant](https://home-assistant.io). Is a modified version of the built-in [samsungtv](https://www.home-assistant.io/integrations/samsungtv/) with some extra features.<br/>
**This plugin is only for 2016+ TVs model!** (maybe all tizen family)

**Development for this project relies solely on donations, so if you enjoy this component, please consider [becoming a patron](https://www.patreon.com/powder_tech) or [donating](https://powder.media/donate) to ensure it's continued survival.**

# Additional Features:

* Ability to send keys using a native Home Assistant service
* Ability to send chained key commands using a native Home Assistant service
* Supports Assistant commands (Google Home, should work with Alexa too, but untested)
* Extended volume control
* Ability to customize source list at media player dropdown list including TV, HDMI sources, apps and specific tv channels
* Cast video URLs to Samsung TV
* Open browser pages on a Samsung TV
* Supports Samsung Frame TVs
* Connect to SmartThings Cloud API for additional features: see TV channel names, see which HDMI source is selected, more key codes to change input source
* Display logos of TV channels (requires Smartthings enabled) and apps

![N|Solid](https://i.imgur.com/8mCGZoO.png)
![N|Solid](https://i.imgur.com/t3e4bJB.png)
![N|Solid](https://i.imgur.com/2dVt9aO.png)
![N|Solid](https://i.imgur.com/tPnv4C9.png)

# Installation

### 1. Easy Mode

Install via HACS.


### 2. Manual

Install it as you would do with any homeassistant custom component:

1. Download `custom_components` folder.
2. Copy the `samsungtv_tizen` directory within the `custom_components` directory of your homeassistant installation. The `custom_components` directory resides within your homeassistant configuration directory.
**Note**: if the custom_components directory does not exist, you need to create it.
After a correct installation, your configuration directory should look like the following.
    ```
    └── ...
    └── configuration.yaml
    └── custom_components
        └── samsungtv_tizen
            └── __init__.py
            └── media_player.py
            └── websockets.py
            └── shortcuts.py
            └── smartthings.py
            └── upnp.py
            └── exceptions.py
            └── manifest.json
    ```


# Configuration

1. Enable the component by editing the configuration.yaml file (within the config directory as well).
Edit it by adding the following lines:
    ```
    # Example configuration.yaml entry
    media_player:
      - platform: samsungtv_tizen
        host: IP_ADDRESS
        mac: MAC_ADDRESS
    ```
    **Note**: This is the same as the configuration for the built-in [Samsung Smart TV](https://www.home-assistant.io/integrations/samsungtv/) component.

    ### Custom configuration variables

    **name:**<br/>
    (string)(Optional)<br/>
    A name for your TV, this will also be used for the entity ID<br/>
    
    **update_method:**<br/>
    (string)(Optional)<br/>
    This change the ping method used for state update. Values: "ping", "websockets" and "smartthings"<br/>
    Default value: "ping"<br/>
    Example value: "websockets"<br/>
    
    **update_custom_ping_url:**<br/>
    (string)(Optional)<br/>
    Use custom endpoint to ping.<br/>
    Default value: PING TO 8001 ENDPOINT<br/>
    Example value: "http://192.168.1.77:9197/dmr"<br/>
    
    **source_list:**<br/>
    (json)(Optional)<br/>
    This contains the KEYS visible sources in the dropdown list in media player UI.<br/>
    Default value: '{"TV": "KEY_TV", "HDMI": "KEY_HDMI"}'<br/>
    You can also chain KEYS, example: '{"TV": "KEY_SOURCES+KEY_ENTER"}'<br/>
    And even add delays (in milliseconds) between sending KEYS, example:<br/>
    '{"TV": "KEY_SOURCES+500+KEY_ENTER"}'<br/>
    Resources: [key codes](https://github.com/jaruba/ha-samsungtv-tizen/blob/master/Key_codes.md) / [key patterns](https://github.com/jaruba/ha-samsungtv-tizen/blob/master/Key_chaining.md)<br/>
    **Warning: changing input source with voice commands only works if you set the device name in `source_list` as one of the whitelisted words that can be seen on [this page](https://web.archive.org/web/20181218120801/https://developers.google.com/actions/reference/smarthome/traits/modes#mode-settings) (under "Mode Settings")**<br/>
    
    **app_list:**<br/>
    (json)(Optional)<br/>
    This contains the APPS visible sources in the dropdown list in media player UI.<br/>
    Default value: AUTOGENERATED<br/>
    Example value: '{"Netflix": "11101200001", "YouTube": "111299001912", "Spotify": "3201606009684"}'<br/>
    Known lists of App IDs: [List 1](https://github.com/tavicu/homebridge-samsung-tizen/issues/26#issuecomment-447424879), [List 2](https://github.com/Ape/samsungctl/issues/75#issuecomment-404941201)<br/>
    **Note: For advanced use of this setting, read the [app_list guide](https://github.com/jaruba/ha-samsungtv-tizen/blob/master/App_list.md)**<br/>
    **Note 2: Although this setting is optional, it is highly recommended (for best performance) to set it manually and not rely on the autogenerated list.**<br/>

    **channel_list:**<br/>
    (json)(Optional)<br/>
    This contains the tv CHANNELS visible sources in the dropdown list in media player UI. Default format is '{"CHANNELNAME":"CHANNELNUMBER","CHANNELNAME":"CHANNELNUMBER"}' for standard channels in TV mode via antenna/coaxial input. Those that use external devices/network based players (e.g. DLNA) can define URLs instead to define the different  channels they wish to have available. To guarantee performance keep the list small, recommended maximum 30 channels.<br/>
    Example value: '{"MTV": "14", "Eurosport": "20", "TLC": "http://url/"}'<br/>

    **api_key:**<br/>
    (string)(Optional)<br/>
    API Key for the SmartThings Cloud API, this is optional but adds better state handling on, off, channel name, hdmi source, and a few new keys: `ST_TV`, `ST_HDMI1`, `ST_HDMI2`, `ST_HDMI3`, etc. (see more at [SmartThings Keys](https://github.com/jaruba/ha-samsungtv-tizen/blob/master/Smartthings.md#smartthings-keys))<br/>
    [How to get an API Key for SmartThings](https://github.com/jaruba/ha-samsungtv-tizen/blob/master/Smartthings.md)<br/>
    _You must set both an `api_key` and `device_id` to enable the SmartThings Cloud API_<br/>
    
    **device_id:**<br/>
    (string)(Optional)<br/>
    Although this is an optional value, it is mandatory if you've set a SmartThings API Key in order to identify your device in the API.<br/>
    [How to get a device ID from SmartThings](https://github.com/jaruba/ha-samsungtv-tizen/blob/master/Smartthings.md)<br/>
    _You must set both an `api_key` and `device_id` to enable the SmartThings Cloud API_<br/>

    **show_channel_number:**<br/>
    (boolean)(Optional)<br/>
    If the SmartThings API is enabled (by settings "api_key" and "device_id"), then the TV Channel Names will show as media titles, by setting this to True the TV Channel Number will also be attached to the end of the media title. (when applicable)

    **scan_app_http:**<br/>
    (boolean)(Optional)<br/>
    This option is `True` by default. In some cases (if numerical IDs are used when setting `app_list`) HTTP polling will be used (1 request per app) to get the running app.<br/>
    This is a lengthy task that some may want to disable, you can do so by setting this option to `False`.<br/>
    For more information about how we get the running app, read the [app_list guide](https://github.com/jaruba/ha-samsungtv-tizen/blob/master/App_list.md).<br/>

    **is_frame_tv:**<br/>
    (boolean)(Optional)<br/>
    This option is `False` by default. The "Frame" model of Samsung TVs have an Art Mode feature, if this option is set to `True` the component will hold the power key for 3 seconds to turn off this TV model (instead of pressing normally which would go to Art Mode)<br/>

    **show_logos:**<br/>
    (string)(Optional)<br/>
    The background color and channel / service logo preference to use, example: "white-color" (background: white, logo: color). "none" to disable.<br/>
    Supported values: "none", "white-color", "dark-white", "blue-color", "blue-white", "darkblue-white", "transparent-color", "transparent-white"<br/>
    Default value: "white-color" (background: white, logo: color)<br/>
    Notice that your logo is missing or outdated? In case of a missing TV channel logo also make sure you have Smartthings enabled. This is required for the component to know the name of the TV channel. Check our guide [here](https://github.com/jaruba/ha-samsungtv-tizen/blob/master/Logos.md) for updating the logo database this component is relying on.<br/> 
   
    **broadcast_address:**<br/>
    (string)(Optional)<br/>
    **Do not set this option if you do not know what it does, it can break turning your TV on.**<br/>
    The ip address of the host to send the magic packet (for wakeonlan) to if the "mac" property is also set.<br/>
    Default value: "255.255.255.255"<br/>
    Example value: "192.168.1.255"<br/>


2. Reboot Home Assistant
3. Congrats! You're all set!

# Usage

### Known Supported Voice Commands

**Google Home verified commands**

* Turn on `SAMSUNG-TV-NAME-HERE` (for some older TVs this only works if the TV is connected by LAN cable to the Network)
* Turn off `SAMSUNG-TV-NAME-HERE`
* Volume up on `SAMSUNG-TV-NAME-HERE` (increases volume by 1)
* Volume down on `SAMSUNG-TV-NAME-HERE` (decreases volume by 1)
* Set volume to 50 on `SAMSUNG-TV-NAME-HERE` (sets volume to 50 out of 100)
* Mute `SAMSUNG-TV-NAME-HERE` (sets volume to 0)
* Change input source to `SOURCE-NAME-HERE` on `SAMSUNG-TV-NAME-HERE` (can be any source name including an app, tv channel or HDMI source)

(if you find more supported voice commands, please create an issue so I can add them here)

**Alexa support**

Read our [Alexa Guide](https://github.com/jaruba/ha-samsungtv-tizen/blob/master/Alexa_guide.md)

### Cast to TV

`service: media_player.play_media`

```
{
  "entity_id": "media_player.samsungtv",
  "media_content_type": "url",
  "media_content_id": "FILE_URL",
}
```
_Replace FILE_URL with the url of your file._

### Send Keys
```
service: media_player.play_media
```

```json
{
  "entity_id": "media_player.samsungtv",
  "media_content_type": "send_key",
  "media_content_id": "KEY_CODE",
}
```
**Note**: Change "KEY_CODEKEY" by desired key_code. (also works with key chaining and SmartThings keys: ST_TV, ST_HDMI1, ST_HDMI2, ST_HDMI3, etc. / see more at [SmartThings Keys](https://github.com/jaruba/ha-samsungtv-tizen/blob/master/Smartthings.md#smartthings-keys))

Script example:
```
tv_channel_down:
  alias: Channel down
  sequence:
  - service: media_player.play_media
    data:
      entity_id: media_player.samsung_tv55
      media_content_id: KEY_CHDOWN
      media_content_type: "send_key"
```


***Key Codes***
---------------
To see the complete list of known keys, [check this list](https://github.com/jaruba/ha-samsungtv-tizen/blob/master/Key_codes.md)


***Key Chaining Patterns***
---------------
Key chaining is also supported, which means a pattern of keys can be set by delimiting the keys with the "+" symbol, delays can also be set in milliseconds between the "+" symbols.

[See the list of known Key Chaining Patterns](https://github.com/jaruba/ha-samsungtv-tizen/blob/master/Key_chaining.md)


***Open Browser Page***
---------------

```
service: media_player.play_media
```

```json
{
  "entity_id": "media_player.samsungtv",
  "media_content_type": "browser",
  "media_content_id": "https://www.google.com",
}
```
