# MetroMate — Delhi Commuter Assistant

Auto-orchestrates Uber bookings across your fixed IFFCO Chowk → Saket → Sikanderpur commute so an auto is always waiting when you exit the metro.

---

## Deploy to Vercel (3 steps)

**1. Push the file to a GitHub repo**
```
git init && git add index.html && git commit -m "init"
gh repo create metromate --public --push
```

**2. Import the repo in Vercel**
Go to [vercel.com/new](https://vercel.com/new), click **Import**, select the `metromate` repo, and click **Deploy**. No build settings needed — it's a static HTML file.

**3. Open on your iPhone**
Copy the `*.vercel.app` URL from Vercel, open it in Safari, then **Add to Home Screen** (Share → Add to Home Screen) so it runs full-screen like a native app.

---

## How the app works

| Trigger | What happens |
|---|---|
| Departure time arrives | "Book Auto" button activates for Home → IFFCO Chowk |
| Tap "Boarded at IFFCO Chowk" | Countdown starts: `13.32 min − buffer` |
| Countdown hits 0 | "Book Auto at Saket Gate X" button fires Uber deep link |
| Tap "Boarded at Saket (Return)" | Countdown starts: `8.88 min − buffer` |
| Countdown hits 0 | "Book Auto at Sikanderpur" button fires Uber deep link |

All Uber deep links open the Uber app with the pickup pre-filled. Leg 1 sets IFFCO Chowk as the dropoff; legs 2 and 3 set the metro gate / station as the pickup.

---

## Adding Google Maps API later

In `index.html`, search for the comment:
```
// TODO [Google Maps API]
```

Three integration points are marked:

1. **Dynamic stop counts** — replace `ROUTE_IFFCO_SAKET.stops` and `ROUTE_SAKET_SIKANDERPUR.stops` with a Directions API call (`mode=transit`) that returns the actual stop count between two stations.

2. **Precise gate coordinates** — replace `SAKET_GATES` entries with Places Details API lookups using the `place_id` for each gate exit (they appear as separate POIs on Google Maps).

3. **Live train timing** — replace `STOP_INTERVAL_MIN` (currently a fixed 2.22 min average) with a DMRC departure-board feed so the countdown reflects the actual next train.

To add a Maps API key once you have one:
```html
<script src="https://maps.googleapis.com/maps/api/js?key=YOUR_KEY&libraries=places"></script>
```
Add this just before the closing `</body>` tag. The TODO comments in the JS will guide you from there.

---

## Settings (localStorage keys)

| Key | Default | Description |
|---|---|---|
| `mm_departureTime` | `08:20` | HH:MM departure from home |
| `mm_bufferTime` | `5` | Minutes before arrival to book auto |
| `mm_saketGate` | `1` | Gate number 1–5 |
| `mm_sikDestination` | `""` | Free-text destination label near Sikanderpur |
| `mm_s1 / mm_s2 / mm_s3` | `waiting` | Per-leg state (waiting/active/ready/booked) |
| `mm_t2 / mm_t3` | `null` | Timestamps when legs 2 and 3 were started |

Clear all keys with **Reset commute** on the commute screen, or open browser DevTools → Application → Local Storage and delete the `mm_*` keys.
