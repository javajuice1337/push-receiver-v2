> [!WARNING]
> This library is no longer functioning since September 2024, and is not maintained. Use this instead: https://github.com/liamcottle/push-receiver

# push-receiver-v2

A library to subscribe to GCM/FCM and receive notifications within a node process. v2 is compatible with the latest FCM registration endpoints.

The original [push-receiver](https://github.com/MatthieuLemoine/push-receiver) FCM registration endpoint has been [deprecated and removed as of June 20, 2024](https://firebase.google.com/support/faq#fcm-depr-features).

## When should I use `push-receiver-v2` ?

- I want to **receive** push notifications sent using Firebase Cloud Messaging in a NodeJS application.
- I want to communicate with a node process/server using Firebase Cloud Messaging infrastructure.

## When should I not use `push-receiver-v2` ?

- I want to **send** push notifications (use the firebase SDK instead)
- My application is running on a FCM supported platform (Android, iOS, Web).

## Install

`
npm i -S push-receiver-v2
`

## Requirements

- Node v8 (async/await support)
- Firebase apiKey, appID, and projectID to receive notification
- Firebase serverKey to send notification (optional) ([deprecated and removed as of June 20, 2024](https://firebase.google.com/support/faq#fcm-depr-features))

## Usage

```javascript
const { register, listen } = require('push-receiver-v2');

// Firebase config
const config = {
  firebase: {
    apiKey: "XXxxXxX0x0x-Xxxx0-X0Xxxxx_0xxXx_XX0xXxX",
    appID: "1:000000000000:android:xxx0xxxx0000x000xxx000",
    projectID: "the-app-name"
  },
  vapidKey: '' // optional
};

// First time
// Register to GCM and FCM
const credentials = await register(config); // You should call register only once and then store the credentials somewhere
storeCredentials(credentials) // Store credentials to use it later
const fcmToken = credentials.fcm.token; // Token to use to send notifications
sendTokenToBackendOrWhatever(fcmToken);

// Next times
const credentials = getSavedCredentials() // get your saved credentials from somewhere (file, db, etc...)
// persistentIds is the list of notification ids received to avoid receiving all already received notifications on start.
const persistentIds = getPersistentIds() || [] // get all previous persistentIds from somewhere (file, db, etc...)
await listen({ ...credentials, persistentIds}, onNotification);

// Called on new notification
function onNotification({ notification, persistentId }) {
  // Update list of persistentId in file/db/...
  updatePersistentIds([...persistentIds, persistentId]);
  // Do someting with the notification
  display(notification)
}
```

### Test notification

To test, you can use the [send script](scripts/send/index.js) provided in this repo, you need to pass your serverKey and the FCM token as arguments :

```
node scripts/send --serverKey="<FIREBASE_SERVER_KEY>" --token="<FIREBASE_TOKEN>"
```

Note: The `send` endpoint is deprecated and removed as of June 20, 2024: https://firebase.google.com/support/faq#fcm-depr-features

# Acknowledgements

- [push-receiver](https://github.com/MatthieuLemoine/push-receiver) for the original implementation.
- [Aracna FCM](https://github.com/queelag/fcm) for the latest FCM registration endpoints.
