# EasyLink Privacy Policy

Last updated: October 31, 2026

This policy applies to the EasyLink iOS app and its Share, Shortcuts, and notification extensions. EasyLink is client software. Clipboard synchronization and notification services are provided by the EasyLink-compatible server configured by the user. The project maintainer does not operate a shared clipboard or notification server for this app and does not receive users' clipboard content through the app.

## 1. Information We Handle

### Information Stored on Your Device

- The server address, API key, and alias you add are stored in the iOS app's shared local container for use by the main app, Share extension, Shortcuts, and notification features. Keep your API key private: anyone with access to it may be able to access the associated server as you.
- Notification history, clipboard-history metadata, shortcut/notification processing state, and necessary synchronization identifiers may be stored on your device or in the app's shared container.
- Clipboard-history metadata is cached for up to the latest 200 records. History files downloaded on demand are cached on the device, subject to a 200 MB limit or removal after 30 days; when the size limit is exceeded, least-recently-accessed files are removed first.

### Clipboard Content and Files You Choose to Sync

EasyLink does not continuously monitor or read the clipboard in the background. It processes clipboard content when you tap an upload, download, or share action, or run a corresponding Shortcut. Text, images, videos, and files that you select or share, together with synchronization metadata such as file names, sizes, content hashes, and timestamps, are sent to the currently selected server.

Large files may be uploaded or downloaded directly through a temporary transfer URL issued by that server. The URL may point to an object-storage service configured by the server operator and may use a different host from the API server. The server and storage operators determine the transfer destination, server-side retention, backups, and deletion practices.

### Notifications and Device Information

If you enable notifications, EasyLink registers your Apple push-notification token, stable installation/route identifiers, the device identifier returned by the server, and notification capabilities and synchronization state with the currently selected server so it can deliver notifications to this device. Notification titles, bodies, groups, and other content you submit are sent to that server. The server may then deliver pushes through Apple Push Notification service (APNs). Apple processes the push token, payload, and necessary network information under Apple's own privacy policy.

Notification history is stored on your device. Actions such as viewing, clicking, or reading a notification may be sent to the currently selected server. If a notification contains an external image URL, the notification extension may download the image from that URL when processing the notification; the URL operator may receive ordinary request information such as your IP address and request time.

### Network and Third-Party Services

- EasyLink sends your API key in an authorization request header to the server you configured. It does not send the key to a server operated by the EasyLink project maintainer.
- EasyLink does not include advertising SDKs or third-party analytics/tracking SDKs. Apple may process App Store, TestFlight, APNs, and system-diagnostic information according to your device settings and Apple's terms.
- Services such as GitHub, iCloud Shortcuts, or websites linked from notifications process access information under their own privacy policies when you choose to open them.
- If your configured server uses external object storage or other third-party services, their data processing is controlled by the server operator and those service providers.

## 2. Purposes and Sharing

The information above is used to retain your settings, synchronize clipboard content you choose, transfer files, deliver notifications, display history, and maintain device registration and notification receipts. The EasyLink project maintainer does not sell user data or use it for advertising profiles. Your selected server and its operator receive the information needed for the features you request; APNs and object-storage services configured by the server may also participate in delivery or transfer.

## 3. Retention and Deletion

- Server settings, notification history, and other local data remain on the device until you delete or clear them in the app, or uninstall the app and iOS removes its app data. Whether the iOS system clipboard syncs through iCloud to your other devices is controlled by your Apple device settings.
- Clipboard-history files are pruned according to the size and age limits in Section 1. Clipboard-history metadata is limited to the latest 200 records.
- Clipboard content, files, notifications, and device records on the server are controlled by the operator of the server you selected. Use the in-app clear/delete features where available. To delete server backups or data the app cannot remove, contact that server's operator. The EasyLink project maintainer cannot directly access or delete data on a self-hosted server.
- You can revoke EasyLink's notification permission in iOS Settings and remove API keys/server settings in the app. Removing an API key does not necessarily immediately delete data or device records already stored on the server.

## 4. Security

EasyLink uses the iOS app sandbox and App Group to restrict local data access. Network encryption depends on the server address you configure: HTTPS protects data in transit, while HTTP does not; the app does not force every custom server to use HTTPS. The security of a server and its object storage is the responsibility of their operators. Do not store sensitive information on a server you do not trust.

## 5. Children's Privacy

EasyLink is not designed for children. The app does not intentionally collect children's personal information.

## 6. Changes and Contact

We will update this page and its “Last updated” date if the app's data practices change. For privacy inquiries, use the developer contact channel listed on the App Store product page. The GitHub repository issue tracker is public; do not post API keys, clipboard content, or other sensitive personal information there. You may contact the maintainer through the [Release repository issue tracker](https://github.com/feranwq/easylink-releases/issues).

GitHub project: [feranwq/easylink-releases](https://github.com/feranwq/easylink-releases)

中文版：[EasyLink 隐私政策](../privacy-policy.md)
