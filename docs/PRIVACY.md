# Privacy Policy — TabiFlow

**Last updated: 28 September 2026 · App version 1.1.0**

This document describes exactly what TabiFlow stores, where, and who can read it.
It is written to be read, not to be skimmed past. If anything here is unclear,
that is a defect in this document — please report it.

> **Translation status.** This policy is authoritative in **Italian** and
> **English**. German, Spanish, Portuguese and Ukrainian versions are not yet
> published: the in-app link opens this single English page, whatever your app
> language is. `privacyPolicyUrl` is one fixed URL with no language parameter and
> there is no per-language file, so the app has nothing to choose between. **A
> native-speaker translation is required before those locales are promoted in
> any store listing.**

---

## 1. In short

- TabiFlow has **no analytics, no advertising, no tracking, no profiling, and
  no third-party behavioural SDK**.
- Your trips live in **your own Firebase account** (or, in guest mode, **only on
  your device**). We operate no server of our own.
- The AI assistant is **optional** and off until you turn it on. When you use
  it, the relevant trip text is sent to **Google** (via Firebase AI Logic) to
  produce an answer, and the prompt transits Google's infrastructure.
- The AI path needs **no API key**: it authenticates with your Firebase account
  and App Check. A Gemini API key can be saved as a retained fallback, and is
  **never transmitted to us** — it is read directly by your device and sent only
  to Google.

## 2. Who is responsible

TabiFlow is a personal, independently developed application. There is no
company behind it and no data controller receiving your data. Data is stored
under a Google Cloud Firebase project used solely by this app.

**Contact for any privacy question, deletion request, or incident:**
open an issue on the project's public repository,
<https://github.com/Fabbro96/TabiFlow-releases/issues>.

## 3. What data the app stores

Only what the app needs to show you your own itinerary.

| Category | Concrete fields |
|---|---|
| Account | Firebase authentication identifier, display name, email address, profile photo URL — supplied by Google when you sign in, and visible to you in Settings |
| Trips | Title, destination, start and end date, day count, currency code and symbol, exchange rate to euro, dietary notes, per-trip visibility setting |
| Days and stops | Day number and date, stop title, description, time slot, estimated cost, category, geographic coordinates, visit/booking flags, an optional Google Maps link you type yourself |
| Collaborators | The email addresses you invite, and their role (owner, editor, viewer) |
| AI chat | Your messages and the assistant's replies, held in memory for the current session; not persisted to the cloud |
| Device cache | The above trip data plus cached weather, exchange rates and currency metadata, kept in app-private storage so the app works offline |

**Special categories of data.** The app has no field for health, religion,
ethnicity, biometrics or political opinion. `dietary notes` is free text you
control; do not put sensitive information in it unless you want it shared with
your collaborators.

## 4. Where data is stored, and who can read it

**Signed in.** Trips are stored in Cloud Firestore in your account. Read
access is enforced by Firestore security rules, not by the app: a collaborator
can only read a trip after the owner adds their email address, and a viewer
cannot write. The rules are part of the source code and are covered by tests.

**Guest mode.** If you choose "Continue as Guest", nothing is uploaded at all.
Trips are written to app-private storage on your device only. **Anyone with
physical access to an unlocked device can read them**, and uninstalling the app
may not reliably erase them.

**Never public.** The app does not create publicly readable trips. If a trip
document predates this and is still marked public, that is a migration defect,
not a feature.

## 4.1 Third-party services you contact

The app is not self-contained. These are the only external services it contacts
on its own, and what each one receives. None of them receives your account
identity or your trips' text, with the single exception of Gemini, which is
described in §5 and happens only if you gave the app a key.

| Service | What is sent | When |
|---|---|---|
| `tile.openstreetmap.org` (OpenStreetMap Foundation, volunteers) | The map tile coordinates you are viewing — that is, the geographic area of the trip you are looking at — with a `User-Agent` identifying this app and its public repository | Only while a trip map is open |
| `api.open-meteo.com` (Open-Meteo) | The latitude and longitude of your stops, to retrieve weather | Only when you open a view that shows weather |
| `api.frankfurter.dev`, or `open.er-api.com` if the first one fails | A single hard-coded query for euro-based rates (`?base=EUR`). No trip data, no identifier, and nothing that identifies you | Only when a trip's exchange rate is set or refreshed |
| `generativelanguage.googleapis.com` (Google) | Your AI question plus the trip context described in §5 — and only if you supplied an API key | Only when you use the AI assistant |
| `api.github.com` (GitHub) | The current app version of the app | Only on the automatic update check |
| `github.com`, `objects.githubusercontent.com`, `release-assets.githubusercontent.com` (GitHub) | The update file itself, downloaded only when you accept an update. The download is redirected by GitHub to one of its asset hosts, and the app allows no other host | Only when you accept an update |
| Firebase (Google) | Your account, your trips, your days and your stops, when signed in | Continuously, while signed in |

**Links you tap are not silent.** When you tap a stop's map link, the OSM
attribution, this page, or the AI Studio link, the app hands the URL to your
system browser or to another app, and *that* service sees the request under its
own policy. The app checks the scheme before opening anything, so the value can
only ever be `https`, a `geo:` or a `google.navigation:` link — never a
`javascript:`, `intent:` or `file:` one.

Two of these deserve emphasis:

- **The tile request reveals the area you are looking at.** Opening a map for a
  trip tells the OpenStreetMap Foundation which part of the world you are
  planning. It cannot see your stops, your dates or your identity, but it does
  see the coordinates, and the tile provider's logs retain them under their own
  retention policy, which is outside this app's control.
- **OpenStreetMap tiles are donated infrastructure.** The Foundation funds them
  from donations and asks that apps identify themselves and stay within a
  reasonable volume. This app sends a distinctive `User-Agent` and shows the
  required attribution. If it ever becomes unavailable, the map will show an
  explicit error rather than an empty rectangle.


## 5. The AI assistant

The assistant is **off by default**. When it is off, or when its AI backend is
unreachable, TabiFlow falls back to a local heuristic that sorts your stops and
estimates their category and cost on-device. On that path **no trip data leaves
your device**.

**The AI path does not require an API key.** The app calls **Firebase AI Logic**
(`firebase_ai`), which authenticates the request with your Firebase account and
Firebase App Check. There is no Gemini API key on that path — nothing stored on
the device, nothing for a same-origin script to read. The Gemini API key field in
**Settings** is a retained fallback and no longer enables the assistant; see
§5.1.1.

When the assistant runs, the app sends the following to **Google**, to produce a
response: your question, and the context the answer needs — your trip
destination, dates, currency, dietary notes, and the titles, descriptions and
times of the stops in the day you are asking about. It does **not** send your
email address, your account identifier, or any other trip's data.

**Prompts transit Google's infrastructure on the AI path.** Firebase AI Logic
forwards the prompt to Google's Gemini models, and Google's own terms and data
processing terms apply to that request, in addition to this policy. The trip
context described above therefore leaves your device, and reaches Google, on
every AI request. The on-device heuristic is the only path on which nothing
leaves the device.

This context is inserted with explicit delimiters and an instruction to treat it
as data rather than as instructions. That is a mitigation, not a guarantee: no
prompt is proof against injection. Treat the assistant's output as a suggestion,
and check anything it tells you before acting on it.

A Gemini API key you have saved is stored **on your device only** and is sent
only to Google. It is never transmitted to us, and we have no access to it.

**When the AI backend fails, the app says so.** The heuristic result is
presented with a visible notice that it was not an AI answer, together with the
server's own error message. This is deliberate: a heuristic itinerary is
plausible, and without that notice a misconfigured backend would be
indistinguishable from a model that simply took a while. If you see that
notice, the route you are looking at was not produced or ordered by the model.

### 5.1 Specific warning for the web version

This warning is about the **retained Gemini API key fallback**, which still
exists and is still stored on the web client. It is not softened because the
primary path no longer needs the key: the fallback is real, and a key you have
saved is exposed the same way it always was.

On the **web** version, the browser's local storage has no hardware-backed
protection. The key and its decryption material sit in browser storage, where
**any script running on the same origin can read them**. The Content-Security-Policy
shipped with the app is the control that limits this, and it is strict — but it
is a mitigation, not a guarantee.

**Therefore: on web, if you save a Gemini API key at all, use one restricted to
the Gemini API, with a usage quota you are willing to lose.** On Android the key
is stored in hardware-backed encrypted storage and this warning does not apply.

#### 5.1.1 The assistant runs on Firebase AI Logic, and the key is a retained fallback

The app's primary AI backend is **Firebase AI Logic** (`firebase_ai`), which
authenticates with your Firebase account and Firebase App Check. On that path
there is **no API key stored on the device at all** — nothing to leak from
`localStorage`, and no key for a same-origin script to read.

The Gemini API key field in **Settings** has been kept as an explicit fallback,
and it is still stored where §5.1 describes, so **the exposure described above
is reduced but not eliminated**. It is not eliminated for two separate reasons:

1. The key is still stored on the web client, where `flutter_secure_storage`
   encryption is cosmetic, as §5.1 says.
2. We have **not verified** that Firebase AI Logic is enabled and App Check is
   enforced for this project. Deleting the only AI path on the strength of a
   console setting we cannot see from the build would risk shipping a dead
   feature, which is a worse outcome than the exposure we are reducing.

The key therefore no longer enables the assistant: it is stored, and the
Settings field says so. It will be removed once AI Logic and App Check are
confirmed working in production; the code and `docs/PRIVACY.md` both mark that
as the gate. On Android the exposure was already limited by hardware-backed
storage, and it will be removed on the same schedule.

What this means for your data: **prompts are still sent to Google**, now through
Firebase AI Logic, with the same trip context described in §5 and the same
explicit data/instruction delimiters. The transport changed; the destination
did not.

#### 5.1.2 App Check is a release-time setting, not a build-time one

Firebase AI Logic refuses requests unless **App Check** is enforced for the
project. The app activates App Check itself at startup — automatically on
Android, and on web when the build was made with a web site key. A **web release
built without `--dart-define=FIREBASE_APPCHECK_WEB_SITE_KEY=<site key>` will
therefore make every AI request fail**, and the app will say so on screen rather
than quietly falling back.

App Check itself collects a small amount of device or browser attestation
signal, handled by Google under its own terms. It is what makes the AI path
usable without an API key, so it is part of the AI path and not of the
on-device heuristic.

## 6. Sharing

You choose who else can see a trip, by inviting email addresses one at a time.
An invited person receives, by role:

- **Viewer** — read the trip, its days and its stops
- **Editor** — the above, plus add, edit and reorder stops
- **Owner** — the above, plus delete the trip and manage collaborators

Inviting someone grants them access to that trip's data. Removing them revokes
it, subject to Firestore propagating the change. We never share trip content
with anyone you have not invited.

## 7. What we never do

- No advertising, no third-party trackers, no analytics SDK, no fingerprinting.
- No sale or rental of data. There is nothing to sell: we do not receive it.
- No automated profiling or decision-making about you.
- No use of your content to train a model. Requests to Google Gemini are subject
  to Google's own terms, which are outside this app's control.

## 8. Your rights and how to exercise them

- **See your data** — every field is visible in the app.
- **Correct it** — edit any trip, day, stop or note in the app.
- **Delete a trip** — the trash action on the trip card. This removes it from
  the cloud and from collaborators' views.
- **Delete everything** — remove a trip *and* sign out. In guest mode, deleting
  each trip from within the app is sufficient, since nothing was uploaded.
- **Delete your account** — delete it in your Google account settings; the app
  loses access immediately and your trips become unreachable.

We hold no backup of your data outside Firebase, so deletion propagates without
a retention window on our side.

## 9. Children

TabiFlow is not directed at children under 13 and is not intended for them.

## 10. Changes to this policy

Any change is versioned with the app and reflected in the "last updated" date
above. Because the app is distributed through app stores, a materially
different policy cannot be pushed silently to an existing installation without
the store surfacing the update.
