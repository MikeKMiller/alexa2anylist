# alexa2anylist

Sync Alexa shopping list with Anylist, the hard way (thanks, Amazon!).

This will do a two-way sync between Anylist and Alexa lists. It will do it's best
to reconcile changes from either side, but if it's unable to figure it out,
the list from Anylist will overwrite the one on Alexa.

## Setup

Create a config file like called `config.json`, like this one:

```json
{
    "amazon_url": "amazon.es",
    "amazon_username": "email@address.com",
    "amazon_password": "xxxxx",
    "amazon_mfa_secret": "xxxx",
    "anylist_username": "email@address.com",
    "anylist_password": "xxxxx",
    "anylist_list_name": "Anylist List"
}
```

Place it somewhere, like `/data/alexa2anylist/` in the example below:

Run the container like so:

```yaml
...
  alexa2anylist:
    image: alexiri/alexa2anylist:latest
    volumes:
      - /data/alexa2anylist:/config
      - /etc/localtime:/etc/localtime:ro
    environment:
      - TZ=Europe/Madrid
    restart: unless-stopped
```

`restart: unless-stopped` is important, as this container *will* crash for various hard-to-avoid reasons. Upon startup, the synchronization
should continue from where it left off.

The pre-built container is at available on dockerhub: https://hub.docker.com/r/alexiri/alexa2anylist

## Running locally with CloakBrowser (Windows / macOS)

CloakBrowser is an anti-detect browser that avoids Amazon's bot-detection better than headless Chrome.

1. Install dependencies (outside Docker):
   ```
   pip install -r requirements.txt
   ```

2. Launch CloakBrowser with remote debugging enabled, e.g.:
   ```
   "C:\Program Files\CloakBrowser\CloakBrowser.exe" --remote-debugging-port=9222
   ```

3. Set environment variables and run:
   ```
   set CONFIG_PATH=C:\data\alexa2anylist
   set CLOAK_BROWSER_DEBUG_PORT=9222
   set CHROME_DRIVER=C:\path\to\chromedriver.exe
   python server.py
   ```

   `CHROME_DRIVER` should point to the chromedriver that ships with CloakBrowser (same version as its embedded Chromium).  
   `CONFIG_PATH` is the folder containing `config.json`, `journal.json`, and credential cache files.

## Credits

Based on https://github.com/madmachinations/home-assistant-alexa-shopping-list for the
handling of Alexa, and on https://github.com/codetheweb/anylist for the interaction with
Anylist.
