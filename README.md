# react-native-firebase-push-notifications

This is kind of fork of [rn-firebase/notifications] https://rnfirebase.io/docs/v5.x.x/notifications/

Thanks to [Sacheen Dhanjie] https://github.com/sacheen how started that fork.

# Current Status
Still in development and testing.
Tested only on Android

## Getting started

`$ npm install react-native-firebase-push-notifications --save`

### Mostly automatic installation

`$ react-native link react-native-firebase-push-notifications`

## Installtion

### Android

#### Update Android Manifest

Add the following to android/app/src/main/AndroidManifest.xml:

Add permissions:
```
<manifest ...>
  <uses-permission android:name="android.permission.INTERNET" />
  <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
  <uses-permission android:name="android.permission.VIBRATE" />
```

Set app launch mode inside activity props:
```
<activity
  ...
  android:launchMode="singleTop" // or singleTask
>
```

#### Default icon and color (Optional)
Within the application component, add metadata elements to set a default notification icon and color. Android uses these values whenever incoming messages do not explicitly set icon or color.
```
<application ...>
  <!-- Set custom default icon. This is used when no icon is set for incoming notification messages.
       See README(https://goo.gl/l4GJaQ) for more. -->
  <meta-data
    android:name="com.google.firebase.messaging.default_notification_icon"
    android:resource="@drawable/ic_stat_ic_notification" />
  <!-- Set color used with incoming notification messages. This is used when no color is set for the incoming
       notification message. See README(https://goo.gl/6BKBk7) for more. -->
  <meta-data
    android:name="com.google.firebase.messaging.default_notification_color"
    android:resource="@color/colorAccent" />
</application>
```

#### Notification channels (Optional)
From Android 8.0 (API level 26) and higher, notification channels are supported and recommended. FCM provides a default notification channel with basic settings. If you prefer to create and use your own default channel, set default_notification_channel_id to the ID of your notification channel object as shown; FCM will use this value whenever incoming messages do not explicitly set a notification channel.
```
<application ...>
  <meta-data
    android:name="com.google.firebase.messaging.default_notification_channel_id"
    android:value="@string/default_notification_channel_id"/>
</application>
```


#### Scheduled Notifications (Optional)
If you would like to schedule local notifications then you also need to add the following to the application component of android/app/src/main/AndroidManifest.xml:
```
<application ...>
  <receiver android:name="com.afrihost.firebase.notifications.RNFirebaseNotificationReceiver"/>
  <receiver android:enabled="true" android:exported="true"  android:name="com.afrihost.firebase.notifications.RNFirebaseNotificationsRebootReceiver">
    <intent-filter>
      <action android:name="android.intent.action.BOOT_COMPLETED"/>
      <action android:name="android.intent.action.QUICKBOOT_POWERON"/>
      <action android:name="com.htc.intent.action.QUICKBOOT_POWERON"/>
      <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
  </receiver>
</application>
```


## Usage
```javascript

import {notifications} from 'react-native-firebase-push-notifications';
import AndroidChannel from 'react-native-firebase-push-notifications/src/notifications/AndroidChannel';


notifications.onNotification((notification: Notification) => {
// Process your notification as required
console.log('onNotification', notification);
});
notifications.onNotificationOpened((notification: Notification) => {
// Process your notification as required
console.log('onNotificationOpened', notification);
});

const channels = [
new AndroidChannel('1', 'Events', 5).setDescription(
    'Events',
),
new AndroidChannel('2', 'Chats', 5).setDescription('Chat'),
];

//Create the channels
for (let i = 0; i < channels.length; i++) {
const channel = channels[i];
notifications.android.createChannel(channel);
}

const notification = new statics.Notification()
.setNotificationId('notificationId')
.setTitle('My notification title')
.setBody('My notification body')
.setData({
    key1: 'value1',
    key2: 'value2',
});

if (Platform.OS == 'android') {
notification.android.setChannelId('1');
}
notifications.displayNotification(notification);
  
const date = new Date();
date.setMinutes(date.getMinutes() + 5);

notifications.scheduleNotification(notification, {
    fireDate: date.getTime(),
  });

const list = await notifications.getScheduledNotifications();
console.log(list);

notifications.cancelNotification(`notificationId`);
```