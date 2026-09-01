# Room

Ask a room of people a question — rating, multiple choice or written answer — and see the
results the moment the timer runs out. Static page, no backend, no database.

## How it works

The host's browser **is** the server. Participants connect straight to it over WebRTC
(peer-to-peer), so answers never touch a server and nothing is stored anywhere except the
host's own device.

```
  phone ─┐
  phone ─┼─── WebRTC ───▶  host browser  ──▶  localStorage  ──▶  CSV download
  phone ─┘                (holds all state)
```

The only shared service used is PeerJS's free public broker, which does nothing but
introduce two browsers to each other. No answers pass through it.

## Deploy

```bash
git init
git add index.html README.md
git commit -m "Room"
git remote add origin git@github.com:YOUR-USER/room.git
git push -u origin main
```

Then on vercel.com: **Add New → Project → import the repo → Deploy.**
Framework preset: **Other**. No build command, no output directory, no environment variables.

Vercel serves `index.html` from the root. The QR code picks up whatever URL it is served
from, so a scan drops people straight onto the join screen with the code filled in.

## Using it

1. Host opens the site, taps **Start a session**, enters a name.
2. A six-character code and QR appear. Everyone scans or types the code and their name.
3. Host picks an answer type, writes the question, sets the timer
   (15s / 30s / 1m / 2m / no limit) and taps **Ask it now**.
4. Results reveal when the timer ends, when everyone present has answered, or when the
   host taps **Show the answer now**.
5. **Finish and see scores** gives the per-person table and a CSV download.

Marking a right answer on a multiple-choice question makes it a scored question. Rating
and written questions are recorded but never scored.

## Things to know

- **Keep the host tab open.** Close it and the room ends. A host refresh recovers the
  session from localStorage and participants reconnect on their own within a few seconds.
- **Same network is best.** Peer-to-peer works across the internet most of the time, but
  some restrictive corporate or mobile networks block it. Everyone on one office Wi-Fi is
  the reliable case. Adding a TURN server would cover the rest.
- **Comfortable up to roughly 30 people.** Every participant holds a connection to the
  host's browser, so a much larger room will strain one laptop.
- **Names identify people.** Someone who refreshes and re-enters the same name is matched
  back to their existing answers. Two people using the same name are treated as one person.
- Answers are hidden from participants until reveal, including the marked correct choice.
