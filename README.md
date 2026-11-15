# Happy Birthday, Reuben Wettey 🎂

An interactive birthday surprise for **Reuben Wettey**. It's a single-page
experience that walks through a little story — favorite things, memories, photos
and videos — and ends with a birthday cake, a wish, confetti, and a place where
**his loved ones can send him a birthday message** straight to his WhatsApp.

The whole app lives in **`crush-app-update/index.html`** (one file: HTML, CSS and JS).
Media lives next to it in `crush-app-update/images/` plus `blue.mp3`.

## Running it locally

It's fully static, so any static server works. For example:

```powershell
# from the repo root
npx serve crush-app-update
# or, with Node installed:
node -e "const h=require('http'),f=require('fs'),p=require('path');h.createServer((q,r)=>{const fp=p.join('crush-app-update',q.url==='/'?'index.html':q.url.split('?')[0]);f.readFile(fp,(e,d)=>{if(e){r.writeHead(404);return r.end()}r.writeHead(200,{'Content-Type':{'.html':'text/html','.mp3':'audio/mpeg','.png':'image/png','.mp4':'video/mp4'}[p.extname(fp)]||'application/octet-stream'});r.end(d)})}).listen(4173)"
```

Then open <http://localhost:4173/>.

## How the flow works

1. Steps 1–7 — the story: a birthday note, favorite things, photos, game night, videos.
2. Step 8 — **Make A Wish**: a CSS birthday cake with 5 candles. Click each flame
   (or press Enter/Space on it) to blow it out, or hit **Blow Out The Candles** to
   do them all at once. When every candle is out you get confetti and the next step.
3. Steps 9–11 — wishes for the year, a playful "forecast", and the finale with a
   big confetti burst.
4. Step 12 — **Send Reuben A Message**: a loved one types their name and a
   birthday message (textarea + emoji picker + clear).
5. Step 13 — the message is "sealed" and delivered, then a **Send to Reuben on
   WhatsApp** button opens WhatsApp with the message pre-filled, addressed to
   Reuben. The sender just taps send.

## How messages reach Reuben

Delivery is **direct over WhatsApp**: the sender's message (with their name) is
composed and handed to WhatsApp via a `wa.me` link — no server or setup required.

Messages are **also archived in Firestore** (collection `birthdayWishes`) on a
best-effort basis, so there's a record of everything sent. The archive is
fire-and-forget: if it fails (or Firebase isn't configured), the WhatsApp
delivery still works exactly the same.

To make the Firestore archive work, the project needs rules that allow creating
wishes. In the Firebase console → Firestore → Rules:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /birthdayWishes/{wishId} {
      allow create: if true;
    }
  }
}
```

## Configuration

At the top of the script in `index.html`:

- **`REUBEN_WHATSAPP_NUMBER`** — where the "Send to Reuben on WhatsApp" button
  sends the message. Digits only, international format (no `+`, no leading `0`).
  **Set this to Reuben's real number.**
- `FIREBASE_CONFIG` — the (optional) Firebase project used to archive messages.

## Tech

- [Three.js](https://threejs.org/) — 3D teddy bears (with party hats), balloons,
  gift boxes, candles and the moon.
- [GSAP](https://greensock.com/gsap/) — step transitions and the seal animation
  (with `setTimeout` safety nets so nothing gets stuck in a hidden tab).
- [canvas-confetti](https://github.com/catdad/canvas-confetti) — confetti bursts.
- [Tailwind CSS](https://tailwindcss.com/) (CDN) — layout utilities.
- [Firebase Firestore](https://firebase.google.com/docs/firestore) — optional message archive.

## Notes

- Background music is `blue.mp3`; the speaker button in the top-right toggles it.
- The photo/video steps (4–7) currently show empty slots ("Your photo/video goes
  here"). Drop the new files into `crush-app-update/images/` and wire them into the
  matching `<div class="media-placeholder">` in `index.html`.
