# EasyDelivery driver app (Dropbox edition)

Android proof-of-delivery app for drivers, the first generation of EasyDelivery. A driver claims a trip, works through the drops, and at each one checks off the parcels, captures a signature and photo, and confirms they're at the right address. The customer gets a PDF proof of delivery by email.

> **Superseded.** This version used Dropbox folders as the backend so the first client could run it on infrastructure they already had. It was replaced by [EasyDeliveryGCP](https://github.com/IlanAgenbacht1/EasyDeliveryGCP), which talks to a REST API on Google Cloud Run through an offline queue. [EDManager](https://github.com/IlanAgenbacht1/EDManager) is the dispatcher app for this version.

## What it does

- **Trip claiming:** pending trips are JSON files in a customer folder. Starting a trip moves its file into an in-progress folder with an atomic Dropbox move, so it disappears from other drivers' lists.
- **Delivery capture:** parcel scanning (ZXing) with discrepancy flags, a signature pad, photos, and a GPS check that the driver is within 50 m of the address.
- **Proof of delivery:** each completed drop is rendered from HTML to PDF (iText 7) and emailed to the customer (Jakarta Mail).
- **Offline:** captured data is stored in SQLite and synced to Dropbox in the background when there's a connection.

## Stack

Java, Android SDK 34, SQLite, Dropbox Core SDK, iText 7 and html2pdf, Jakarta Mail, ZXing, Google Maps and Location services.

## Building

Keys are read from `local.properties` (gitignored):

```properties
DROPBOX_APP_KEY=...
DROPBOX_APP_SECRET=...
DROPBOX_REFRESH_TOKEN=...
EMAIL_APP_PASSWORD=...
```

Then open in Android Studio and run.

## Why it was replaced

Folders gave atomic claims but made a poor queue: no push updates, so the app polled, and there was no way to query across trips. One shared Dropbox credential on every device was also a liability. The next version moved trip state behind an API with per-device enrolment.
