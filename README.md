Let's produce final. Use high detail. final.Below is a single-file, no-build template you can save as index.html. It is designed for Safari on iPhone → Tesla Bluetooth, with Media Session controls, background playback, search, queue/playlists, history + keep/reject linked by MusicBrainz ID, recommendations from Last.fm/ListenBrainz when configured, and bulk import.

It uses free sources only:

Jamendo — full ad-free streams if you add a free Client ID.
iTunes Search API — 30-second previews, no key required.
MusicBrainz — MBID lookup for keep/reject linking.
Last.fm / ListenBrainz — optional recommendations/feedback.
The “AdShield” layer silently preplays upcoming tracks at volume 0 and can skip known ad segments if a source is marked adLikely. With the default Jamendo/iTunes sources, it mostly prewarms the next track and confirms it is playable without ads.

1) Single-file app: save as index.html
(see attached index.html)

2) Setup guide
A. Get free API keys
Required for best results: Jamendo Client ID
Go to https://jamendo.com/api
Create a free account/app if needed.
Copy your Client ID.
Paste it into the code here:
js
const CONFIG = {
  jamendoClientId: 'PASTE_JAMENDO_CLIENT_ID_HERE',
  ...
};
Jamendo gives full ad-free streams for many songs. Without it, the app still works using iTunes 30-second previews.

Optional: Last.fm API key
Used for recommendations from artists you keep.

Go to https://developer.last.fm
Create an account/app.
Copy your API Key.
Paste it here:
js
lastfmApiKey: 'PASTE_LASTFM_API_KEY_HERE'
Optional: ListenBrainz username + token
Used to send keep/reject feedback and fetch recommendations.

Go to https://listenbrainz.org
Log in.
Open settings/token page.
Generate a token.
Paste your username and token here:
js
listenbrainzUsername: 'your_listenbrainz_username',
listenbrainzToken: 'PASTE_LISTENBRAINZ_TOKEN_HERE'
MusicBrainz
No key needed. The app calls it automatically to link songs by MBID when possible. It is rate-limited, so the code waits about 1 second between lookups.

B. Host over HTTPS
Safari on iPhone works best with a secure URL.

Easiest options:

GitHub Pages
Create a repo.
Upload index.html.
Enable Pages.
Open the https://... URL in Safari on iPhone.
Netlify Drop
Go to https://app.netlify.com/drop
Drag the folder containing index.html.
Use the generated HTTPS URL.
Vercel / Cloudflare Pages
Also fine. Just make sure the final URL is HTTPS.

C. iPhone setup for Tesla Bluetooth
On iPhone, open the app in Safari.
Tap Share → Add to Home Screen.
This helps background playback behave more reliably.
Pair your iPhone with the Tesla over Bluetooth.
In Tesla, select your iPhone as the audio source.
Open the app on iPhone.
Search a song and tap Play once.
iOS requires an initial user gesture before reliable background audio.
After that:

Lock screen controls should appear.
Tesla steering wheel next/previous should work if supported by your Tesla/iPhone combo.
Playback should continue when the iPhone screen is locked.
3) Test checklist
Use this on an actual iPhone in Safari, connected to a Tesla or another Bluetooth speaker.

Basic playback
 Page loads over HTTPS on iPhone Safari.
 Search returns results.
 Tap Play and audio starts through the Tesla speakers.
 Lock screen shows title, artist, artwork, play/pause, next/previous.
 Tesla steering wheel controls work if supported.
 Screen can be locked and playback continues.
Queue
 Add multiple songs to queue.
 Play all starts the first queued song.
 Next automatically advances when a track ends.
 Remove an item from the queue without breaking playback.
 Clear queue stops playback and resets player UI.
Playlists
 Create a playlist.
 Add songs to a playlist using +PL.
 Play a playlist.
 Delete a playlist.
History / keep-reject
 Playing a song adds it to history.
 Keep button marks the song as kept.
 Reject button marks the song as rejected.
 MusicBrainz ID appears after a short delay when available.
 If ListenBrainz token is set, keep/reject sends feedback without crashing.
Recommendations
 With Last.fm key: Refresh loads recommendations from kept artists.
 With ListenBrainz username/token: Refresh attempts ListenBrainz recommendations.
 Without either: fallback recommendations load from kept/played artists via iTunes.
 Recommendation songs can be queued or played.
Bulk import
 Paste lines in Artist - Title format.
 Import adds matched songs to the queue.
 Log shows queued/not found/error per line.
AdShield / preroll
 While a song is playing, status badge changes to something like:
AdShield checking next…
AdShield ready (no ads detected)
 You do not hear the next track before it becomes current.
 If you add an ad-prone source with adLikely: true and trackStart, the app seeks to the song start when playing that track.
Failure handling
 If a stream fails, status shows an error and the app tries the next track.
 If Jamendo is configured but network/JSONP fails, iTunes previews still appear.
 App does not crash if Last.fm or ListenBrainz are unconfigured.
Important practical notes
iTunes results are 30-second previews.

This is the no-key fallback. For full songs, add a Jamendo Client ID.

Jamendo stream URLs can expire.

The app refreshes Jamendo streams older than 1 hour before playing them.

AdShield is safest with maxPrerollTracks: 1.

You can set it to 2, but on iPhone that uses more memory/network and may be less stable.

For true ad-prone sources, you would add a source function that returns track objects like:

js
{
  title: "Song",
  artist: "Artist",
  url: "https://example.com/stream.m3u8",
  adLikely: true,
  trackStart: 32 // seconds where the real song starts
}
Then AdShield will silently play through the first trackStart seconds in the background and seek to the song start when that track becomes current.