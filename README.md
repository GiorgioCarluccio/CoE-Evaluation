# Assegnazione attività · OpenEconomics

Live dashboard to allocate people to activities, with shared real-time state.
The published page is [`index.html`](index.html).

- **Viewers**: open the public URL and see the current allocation, updating live.
- **Editing**: anyone can drag disks and tap to change effort — changes sync to everyone instantly.
- **Hosting**: GitHub Pages (static). Shared state lives in **Firebase Realtime Database**.

> Until Firebase is configured, the page runs in *local-only* mode (a yellow banner shows):
> it works, but each browser keeps its own private copy. Configure Firebase to share state.

---

## 1. Create the Firebase project (one time, ~5 min, free)

1. Go to <https://console.firebase.google.com/> → **Add project** (e.g. `coe-allocation`).
   You can skip Google Analytics.
2. In the left menu: **Build → Realtime Database → Create Database**.
   - Pick a location (e.g. *europe-west1*).
   - Start in **locked mode** (we set rules below).
3. Register a **Web app**: Project settings (gear icon) → **Your apps** → **`</>`** (Web).
   Give it a nickname; **don't** enable Hosting. Copy the `firebaseConfig` object shown.

### Set the database rules (open read/write — matches "anyone can edit")

In **Realtime Database → Rules**, paste this and **Publish**:

```json
{
  "rules": {
    "allocation": {
      ".read": true,
      ".write": true
    }
  }
}
```

> This makes the `allocation` node publicly readable and writable — appropriate for an
> internal team tool where anyone may edit. To later restrict editing, replace `.write`
> with an auth condition and add Firebase Auth.

## 2. Paste the config into `index.html`

Near the top of the `<script type="module">` block there is a `firebaseConfig` object with
`PASTE_…` placeholders. Replace it with the values from step 1. These values are **public**
and safe to commit (security is enforced by the database rules above).

Commit and push the change. The yellow banner disappears once configured.

## 3. Enable GitHub Pages

1. On GitHub: **Settings → Pages**.
2. **Build and deployment → Source**: *Deploy from a branch*.
3. **Branch**: `main`, folder `/ (root)` → **Save**.
4. After a minute the site is live at:
   `https://giorgiocarluccio.github.io/CoE-Evaluation/`

Share that URL with external users for read/monitor; they can also edit (open editing).

---

## Notes

- The old standalone file `OE_attività_risorse.html` is kept as a backup; it is **not**
  shared-state. The live, synced version is `index.html`.
- State is stored in the database under `allocation/v3` as `{ placements, efforts, updatedAt }`.
- The status pill in the header shows: *sincronizzato* (synced), *salvataggio…* (saving),
  or *offline* (network/rules issue — changes fall back to local storage).
