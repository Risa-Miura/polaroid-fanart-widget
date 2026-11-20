# Polaroid Fanart Gallery 📸

A StreamElements overlay widget that shows fan art on stream as little polaroids, with the
artist credited on each one. New polaroids drop into random spots without covering each
other, and you can mark areas (like the chat box) where they shouldn't land.

![Six polaroids scattered on a corkboard, each with the artist's credit on its white strip](docs/screenshots/result.png)

That's the local preview with the sample art. The corkboard is just the preview background;
on stream the widget is transparent, so the polaroids sit on top of your own scene.

The art rotates on a timer and each picture drops in with a little bounce. Everything is
managed from the widget's settings panel in StreamElements, so there's no extra website or
account to set up.

Current version is 1.1.0. The full design spec is in
[polaroid-fanart-widget-spec.md](polaroid-fanart-widget-spec.md).

## Install

1. In StreamElements, go to **Streaming tools → Overlays** and open your overlay (or make a new one).
2. Click **+ (Add widget) → Static / Custom → Custom widget**.
3. Select the new widget and click **Open editor**.
4. The editor has four tabs. Paste each file from this repo into its tab, replacing what's there:

   | Editor tab | File |
   |------------|------|
   | HTML   | [widget/widget.html](widget/widget.html) |
   | CSS    | [widget/widget.css](widget/widget.css) |
   | JS     | [widget/widget.js](widget/widget.js) |
   | FIELDS | [widget/fields.json](widget/fields.json) |

5. Click **Done**, then **Save** the overlay.

## Position and size

Drag and resize the widget box to wherever you want the polaroids.

With 1 polaroid, it sits in the middle of the box. Tick **Single polaroid: land in a random
spot** if you'd rather it move around.

With 2–6 polaroids, each new one lands in a random free spot and never covers another. If
the box is small they shrink to make room, so give it some space. If you want a messier
board, raise **Max overlap between polaroids (%)**: they stay full size and can cover up to
that much of each other (newest on top), which can hide part of a credit. If they still
don't fit, they shrink just enough.

### Keep-out zones

You can set up to 3 rectangles where polaroids won't land, for things like your chat box or
"starting soon" text. In the **Keep-out zones** section, tick **Zone 1: on**, then set its
left edge, top edge, width and height. They're all percentages of the widget box, so the
zone stays put if you resize the box. A chat column on the right third would be left 67,
top 0, width 33, height 100.

In the StreamElements editor (or with the debug panel on), each zone shows up as a red
dashed box so you can line it up. It never shows on stream.

![Five polaroids on the left two-thirds of the box, with a red dashed keep-out zone covering the right third](docs/screenshots/keep-out-zone.png)

**How much of a polaroid may cover a zone (%)** controls how strict they are: 0 means never
touch a zone, 20 means up to a fifth of a polaroid can poke in. It measures the tilted
polaroid at the tallest photo size, so in practice they stay a bit clearer than the number.

Polaroids shrink if they need to. A single centered polaroid moves to the nearest clear
spot. If there's really no room (huge zones, tiny box), the widget falls back to a grid that
ignores the zones, and the debug panel tells you.

## Adding art

The settings panel has 24 slots, **Artwork 01** to **Artwork 24**, one piece of art each.

1. Open the next empty slot.
2. Click **Image** and upload a PNG or JPG under 30 MB. 1000–2000 px on the long side is plenty.
3. Fill in the **Artist name** and their **Handle**, written however you want it to show
   (`@name on X`, `name.bsky.social`, …).
4. Add a **Caption** if you want one. It goes on the first line of the strip.
5. Leave **Show this artwork** ticked and click **Save**.

Empty slots are skipped. To hide a piece without deleting it, untick **Show this artwork**.
To replace one, upload a new image to the same slot and update the credit. In slot order
mode, you change the order by swapping what's in two slots.

Emoji work in names, handles and captions (they show as one-color symbols to match the
handwriting). Long names push the handle onto its own line and get smaller instead of being
cut off. If a slot has no name or handle, the polaroid says `art by ?` so you notice.

## Settings

| Setting | What it does |
|---------|--------------|
| Polaroids on screen | 1 = a single polaroid. 2–6 = a scattered wall that swaps one out each time. |
| Single polaroid: land in a random spot | With 1 polaroid, each new one lands somewhere random instead of the center. |
| Max overlap between polaroids (%) | With 2–6 polaroids: 0 = no overlap (they shrink to make room). Higher = full size when there's room, and one can cover up to this much of another. 100 = no limit. |
| Polaroid width (px) | How wide each polaroid is. Text, pin and frame scale with it. |
| Max photo height (px) | Tallest the photo can be. Very tall art gets frame-colored bars on the sides instead of being cropped. |
| Seconds per artwork | How often the art changes. |
| Transition length (seconds) | How long the drop-in and fall-away animations take. |
| Order | Shuffle shows every piece once in random order before repeating. Slot order goes 01, 02, 03… |
| Which polaroid changes | With 2–6 polaroids: Oldest first swaps the one that's been up longest. Random swaps any of them, but not the same spot twice in a row. |
| Max tilt (degrees) | How crooked they hang. 0 = straight. |
| Frame color | Color of the polaroid paper. |
| Caption font | Font for the caption and credit (any Google Font). |
| Caption text color | Color of the caption and credit. |
| Show artist handle | Untick to show just the name. |
| Text before artist name | `art by` by default. Change it (`drawn by`) or clear it. |
| Pin style | Pushpin, two strips of tape, or nothing. |
| Pushpin color | Only used with the pushpin style. |
| Shadow strength | How strong the drop shadow is. |
| Keep-out zones | Up to 3 areas polaroids avoid, plus how much they can cover them. See [Keep-out zones](#keep-out-zones). |
| Show debug panel | A troubleshooting box on the widget. Keep it off on stream. |

## If the stream doesn't update

Saving the overlay usually updates OBS on its own. If it doesn't, open the overlay's browser
source properties in OBS and click **Refresh cache of current page**. Still stuck? Turn on
**Show debug panel** and save. It lists the active slots, recent events (like images that
failed to load) and any warnings.

## Adding more slots

1. In [widget/fields.json](widget/fields.json), find the five `slot24_…` entries for `Artwork 24`.
2. Copy them right below, with a comma after the previous last line.
3. Change every `24` to `25`, in the keys (`slot25_image`) and the group name (`"Artwork 25"`).
4. Run it through a JSON validator. One stray comma and StreamElements shows an empty panel.
5. Paste the whole file into the FIELDS tab again. Your uploads stay.

The widget finds new slots on its own, up to slot 99.

## Changelog

The version is on the first line of the debug panel and in `WIDGET_VERSION` at the top of
[widget/widget.js](widget/widget.js).

- 1.1.0: keep-out zones. Re-paste the JS, CSS and FIELDS tabs (your art stays).
- 1.0.0: first release.

## Running it locally

There's no build step. The four files in [widget/](widget/) get pasted in as they are.

To try changes, serve the repo root and open the preview page, which fakes the
StreamElements runtime:

```sh
py -m http.server 8000
# then open http://localhost:8000/preview/preview.html
```

The side panel has the style settings and reloads the widget on every change, and **Next**
skips to the next artwork. You can also set fields in the URL, like
`preview.html?polaroidCount=6&pinStyle=tape`. The sample data is in
[preview/sample-fielddata.json](preview/sample-fielddata.json), and it includes a hidden
slot (09) and a broken image link (10) on purpose. Opening the file directly (`file://`)
doesn't work because the browser blocks the JSON fetch.

The overlay has no `console`, so use the debug panel instead.
