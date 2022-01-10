# Play and Save TTS Messages

## What is this?
This is more or less an answering machine (remember those?) for your TTS messages.  When you play a TTS message that you want saved under certain condtions (ie. nobody is home), you will call the **Play or Save TTS Messsage** script `script.play_or_save_message` instead of calling your tts service directly.  The script will decide whether to play the message immediately, or save it based on the conditions you specify.

Messages are played back using the **Play Saved TTS Messages** script `script.play_saved_tts_messages`.  Set an appropriate trigger (for example when you arrive home) in the **Play Saved Messages** `automation.play_saved_messages` automation to call this script automatically.

Saved messages will survive restarts.

## How do I use it?
### Install This Package
The easiest way to utilize this is to install it as a [package](https://www.home-assistant.io/docs/configuration/packages/).

To enable packages in your configuation, create a folder in your config directory named `packages` and add the following line to your `configuration.yaml` file.

    homeassistant:
      packages: /config/packages

If you already have packages enabled in your configuration, simply download [package_save_tts_messages.yaml](package_save_tts_messages.yaml) to your packages directory.  Don't forget to restart Home Assistant!

### Install Without Pacakges
To utilize this without installing as a package copy the relevant code and paste in an appropriate place in your `configuration.yaml` file.  Everything but the automation is required for Play and Save TTS Messages to function.

### Install HASS-Variables Custom Integration
The [HASS-Variables](https://github.com/Wibias/hass-variables) integration is available on HACS. If you're not using HACS I'll assume you know how to install it manually.

### Adjust Package For Your Configuration
You must change a couple of things in the package to work with your configuration.  **Look for the #TODO tags!**

- **TTS Servce** - The package default is `tts.cloud_say` which is the NabuCasa TTS service.  Change this to whichever TTS service you use.
- **Media Player** - You must change these media player entity_ids to a valid media player entity id in your configuration.
- **Play or Save Condition** - Adjust this condition to decide whether to play or save the TTS message.  The TTS message will play immediately and will not be saved when the condition(s) evaluate to true.
- **Play Saved Messages Trigger** - Adjust this trigger to automatically call **Play Saved TTS Messages**.  You can, of course, call the script directly anytime from a button etc.
- **TTS Message Timeout** - Adjust this for the longest TTS message you may play or your messages may be cut short.  Default is 60 seconds.

### Create or Adjust your TTS Service Calls
Finally, you must create the **Play or Save TTS Messsage** service calls.  Replace any current direct TTS service for messages you want to save or create new ones.

**EXAMPLE**

    - service: tts.cloud_say
      target:
        entity_id: media_player.dining_room_hub
      data:
        message: "This is my message!"

becomes

    - service: script.play_or_save_message
      data:
        message: "This is my message!"

### Skip No Messages Notification
The default configuration will play *"There are no messages waiting for you."* if there are no saved messages when  **Play Saved TTS Messages** is called.  If you wish to skip this message you can specify the `skip_none` parameter in a  **Play or Save TTS Messsage** service call.

    - service: script.play_or_save_message
      data:
        message: "This is my message!"
        skip_none: true

You can also change the default so the message is not played unless `skip_none` is set to `false`.

Change

    skip_none: '{{ skip_none|default(true) }}'

to

    skip_none: '{{ skip_none|default(false) }}'


Then call with `skip_none` set to `false` if you want the no saved messages messaage to play.

    - service: script.play_or_save_message
      data:
        message: "This is my message!"
        skip_none: false

### Clearing Saved Messages
Saved messages can be cleared without playing by calling the **Clear Saved Messages** `script.clear_saved_messages` script.  This can be used if you want to just display saved messages on the front end instead of playing them back via TTS (see markdown card example).

## Lovelace Examples
Using `variable.saved_messages` in an entity card will display the number of saved messages as the state.  The messages are available in the more-info dialogue.

![Entity](screenshots/saved_msg_entity.PNG "Saved Messages Entity")

![More-Info](screenshots/saved_msg_more_info.PNG "Saved Messages More-Info")

See [saved_messages_markdown_card.yaml](saved_messages_markdown_card.yaml) for a sample markdown card for lovelace to display saved messages on the front end.

![Markdown Card](screenshots/saved_msg_markdown.PNG "Saved Messages Markdown Card")