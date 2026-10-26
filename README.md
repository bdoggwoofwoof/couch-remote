# Couch Remote

A free phone remote for LG webOS TVs and Samsung smart TVs (2016 and newer). Plain HTML, no build step, no server. It talks straight to the TV over your home Wi‑Fi.

## Deploy (Vercel)

1. Put this folder in a GitHub repo (e.g. `couch-remote`).
2. In Vercel: **Add New → Project**, import the repo, framework preset **Other**, no build command. Deploy.
3. Open the URL on your phone and add it to your home screen (iPhone: Share → Add to Home Screen; Android: ⋮ → Add to Home screen / Install app).

## First-time setup (once per phone)

1. Find the TV's IP. LG: Settings → All Settings → General (or Network) → Wi‑Fi → Advanced Wi‑Fi Settings. Samsung: Settings → General → Network → Network Status → IP Settings. Tip: reserve that IP in your router so it never changes.
2. In the app, pick the TV brand, enter the IP and expand **"First time? Allow the TV's security certificate"**. Tap the link, accept the browser warning (iPhone: Show Details → visit this website; Android: Advanced → Proceed). A blank/error page afterward is normal.
3. Back in the app, tap **Connect**, then choose **Accept** (LG) or **Allow** (Samsung) on the TV with your normal remote.
4. If the browser asks to access devices on your local network, allow it.

The pairing key is saved on the phone, so after that it connects on its own.

## What it does

- **Remote:** d‑pad, OK, Back, Home, Exit, quick settings, Info (hold arrows/volume to repeat); volume, mute, channel, play/pause/rewind/fast‑forward; power off
- **Favorites row** on the main screen (long‑press any app to add or remove it)
- **Touch pad** with two modes: *Swipe* (swipe = arrow keys, tap = OK, swipe and hold to keep moving) and *Pointer* (mouse cursor, tap to click, scroll strip)
- **Apps & inputs:** a Popular row (YouTube, YouTube TV, Netflix, Disney+, Hulu, Paramount+, Prime Video, HBO Max, Apple TV, Peacock; ones you don't have open the LG app store), every installed app, HDMI inputs, and a searchable channel list on antenna/cable TVs; the running app is highlighted
- **Brand logos** for all the Popular apps are built in: from [Simple Icons](https://simpleicons.org) (CC0), plus Disney+ and Peacock from [Arcticons](https://github.com/Arcticons-Team/Arcticons) (CC BY-SA 4.0). Trademarks belong to their owners.
- **Websites:** buttons that open a site in the TV's web browser (Storage Access is preset; add or remove your own)
- **Typing:** letters go to TV search boxes as you type; mic button for voice search where the browser supports it; number pad
- **"TV is asking for text"** prompt pops up when a search box on the TV is selected
- **Live state:** volume and the app on screen update even when someone uses the physical remote
- **Multiple TVs:** tap the TV name at the top to switch or add one
- Cached app list so it's usable the moment it opens
- Sensitivity slider on the Touch tab (separate for Swipe and Pointer; pointer speeds up on quick flicks)
- Light/dark/auto theme, vibration on/off
- Screen off; "say hi" test toast

## Samsung TVs

Samsung support uses the TV's own remote connection (`wss://<tv>:8002`), so it needs a 2016 or newer Samsung smart TV (Tizen). The certificate step opens `https://<tv-ip>:8002/api/v2/`.

What's different from LG:
- No live volume number or mute state, and no "Now on…" in the header: Samsung doesn't report them.
- **Inputs** shows Source and HDMI 1–4 buttons instead of the TV's own list; no channel list.
- **Typing:** type the whole search, then tap **Enter** to send it (letters don't appear on the TV one at a time).
- The installed-app list comes from the TV on most models. If yours doesn't share it, the Popular buttons still try each service's usual Samsung app ID.
- Touch pad pointer mode works only on models with mouse support; Swipe mode works on all.
- "Say hi" and Screen off are LG-only.
- If the TV never shows Allow, or says no: Settings → General → External Device Manager → Device Connection Manager. Set Access Notification to "First Time Only", and check the phone isn't set to Deny in Device List.

## Other brands

Roku, Sony, Vizio, Android/Google TV and Fire TV can't be controlled from a web page on a phone. Roku only accepts plain‑http requests, which browsers block from an https site; Sony and Vizio need request headers those TVs don't allow from a browser; Android/Google TV and Fire TV need raw network connections a browser can't open. Supporting them would need a small helper program running on a computer at home, or a native app.

## Known limits

- **Can't turn the TV on.** When the TV is fully off its network socket is closed, and browsers can't send Wake‑on‑LAN. Use the physical remote or ThinQ for power‑on.
- Same Wi‑Fi only.
- **iOS 16 and older:** Safari won't use the TV's certificate for the remote's connection even after you accept it. Use Chrome instead (open `https://<tv-ip>:3001` in Chrome, Advanced → Proceed, then open the remote in Chrome).
- On some iPhones the home‑screen icon doesn't share Safari's certificate approval. Do the certificate step from inside the icon: the link opens the TV's address in the app's own window; accept the warning, go back, and it reconnects.
- Very old TVs (pre‑2018 firmware) only listen on the insecure port, which an https page can't reach. Opening the page over plain http (e.g. from a computer on your network) works for those.

## How it works

webOS TVs run a WebSocket API (`wss://<tv>:3001`). The app registers with the same pairing manifest LG's own remote uses, gets a client key, then sends `ssap://` requests (volume, apps, inputs, typing) and button/pointer events over a second "pointer input" socket.
