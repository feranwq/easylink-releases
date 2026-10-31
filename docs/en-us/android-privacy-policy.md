# EasyLink Android Privacy Notice

**Effective date:** 2026-10-31  
**Applies to:** Android APKs downloaded from GitHub Releases and other distribution channels outside Google Play.

## Analytics and Updates

The app does not include third-party product analytics SDKs and does not report app usage analytics at startup.

Automatic update checks are disabled by default. If you manually check for updates or explicitly enable automatic checks in settings, the app contacts GitHub Releases for version information. When you download an update, the APK is downloaded from GitHub.

If you choose to use the FRPC feature and start downloading its component, the app downloads the component for your device architecture from `xupdate.ppps.cn`.

## Sync and Forwarding

Clipboard sync, clipboard history, usage queries, and notification sync send requests and data to the EasyLink server you configure, as needed for the settings and actions you use. Notification sync may run in the background. The API key is used to access that server.

SMS, call, and app-notification forwarding runs only after you grant the relevant permissions, enable rules, and configure a sending channel. The app reads information needed to apply those rules on your device, such as SMS numbers and content, call numbers and status, and the source app and content of notifications. It then sends the information to the destination service you configure. The developer is not the recipient of forwarded content; the app does not send a separate copy to the developer's servers or use it for product analytics. The developer does not operate or control the destination service you choose. Its operator determines how submitted data is processed, retained, and deleted. If you choose a service operated by the developer as a destination, that service will receive the data you submit. Configure only servers and sending channels that you trust.

## Network Event Information

When an Android network-state change triggers a background network-event task, the app uses HTTPS to query `api.ipify.org` and `api6.ipify.org` for your public IPv4/IPv6 addresses. Those services can see the public IP address from which the requests originate. The current implementation makes these queries before checking network-forwarding rules, so they may occur even when no network rule matches. If a network rule matches, the app may send the network type, carrier/SIM information, Wi-Fi name, and IP information to the sending channel you configured.

## Permissions and Local Data

System permissions are used for their corresponding features. You can review or revoke them in Android settings; revoking a permission may make the related feature unavailable. App configuration and history data are stored locally as needed for the features you use. Clearing app data or uninstalling the app may remove this local data.

## Contact

For privacy questions, contact the developer through [EasyLink Android's GitHub Issues](https://github.com/feranwq/easylink-android/issues).
