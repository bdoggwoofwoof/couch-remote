# Couch Remote

A free phone remote for LG webOS TVs. Plain HTML, no build step, no server. It talks straight to the TV over your home Wi‑Fi.

## Deploy (Vercel)

1. Put this folder in a GitHub repo (e.g. `couch-remote`).
2. In Vercel: **Add New → Project**, import the repo, framework preset **Other**, no build command. Deploy.
3. Open the URL on your phone and add it to your home screen (iPhone: Share → Add to Home Screen; Android: ⋮ → Add to Home screen / Install app).

## First-time setup (once per phone)

1. Find the TV's IP: Settings → All Settings → General (or Network) → Wi‑Fi → Advanced Wi‑Fi Settings. Tip: reserve that IP in your router so it never changes.
2. In the app, enter the IP and expand **"First time? Allow the TV's security certificate"**. Tap the link, accept the browser warning (iPhone: Show Details → visit this website; Android: Advanced → Proceed). A blank/error page afterward is normal.
3. Back in the app, tap **Connect**, then choose **Accept** on the TV with your normal remote.
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
- Light/dark/auto theme, pointer speed, vibration on/off
- Screen off; "say hi" test toast

## Known limits

- **Can't turn the TV on.** When the TV is fully off its network socket is closed, and browsers can't send Wake‑on‑LAN. Use the physical remote or ThinQ for power‑on.
- Same Wi‑Fi only.
- **iOS 16 and older:** Safari won't use the TV's certificate for the remote's connection even after you accept it. Use Chrome instead (open `https://<tv-ip>:3001` in Chrome, Advanced → Proceed, then open the remote in Chrome).
- On some iPhones the home‑screen icon doesn't share Safari's certificate approval. Do the certificate step from inside the icon: the link opens the TV's address in the app's own window; accept the warning, go back, and it reconnects.
- Very old TVs (pre‑2018 firmware) only listen on the insecure port, which an https page can't reach. Opening the page over plain http (e.g. from a computer on your network) works for those.

## How it works

webOS TVs run a WebSocket API (`wss://<tv>:3001`). The app registers with the same pairing manifest LG's own remote uses, gets a client key, then sends `ssap://` requests (volume, apps, inputs, typing) and button/pointer events over a second "pointer input" socket.
