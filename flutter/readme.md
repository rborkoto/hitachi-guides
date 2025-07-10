# Datadog Flutter RUM Integration Guide

This document provides step-by-step guidance for integrating Datadog Real User Monitoring (RUM) into your Flutter application.

What you need to know before jumping in  :

1. With Flutter, everything is a widget. Widgets are basically user interface components used to create the UI of the application. In Flutter, the application is itself a widget.
2. Similar to the likes of NPM with Node, Flutter can leverage Packages by other developers to the Flutter and Dart ecosystems. With Dart the file for managing a projects Packages(application or dev dependencies) is a pubspec.yaml file.
3. With Flutter, Datadog RUM is instrumented at the app-level rather than within each individual widget. This means you only need to initialize it once within your main entry file (main.dart), and Datadog automatically provides visibility into your app’s interactions, screens, performance, errors, and user behavior.

## Step 1: Datadog Account Setup

1. Log into your [Datadog Account](https://app.datadoghq.com/).
2. Navigate to **UX Monitoring > RUM Applications**.
3. Create a new application and note the following credentials:
   - **Client Token**
   - **Application ID**

Refer to the [Datadog Account Management documentation](https://docs.datadoghq.com/account_management/) for more details.

---

## Step 2: Add Datadog Flutter SDK 	

Install the Datadog Flutter plugin using this command:

```shell
flutter pub add datadog_flutter_plugin
```

For additional information, refer to the [Datadog Flutter RUM documentation](https://docs.datadoghq.com/real_user_monitoring/flutter/).

---

## Step 3: Initialize Datadog RUM in Flutter App - Example below (cross check code block with documentation link for any new flags/updates)	

Update your app’s entry point (`lib/main.dart`):

```dart
import 'package:flutter/material.dart';
import 'package:datadog_flutter_plugin/datadog_flutter_plugin.dart';

Future<void> main() async {
  WidgetsFlutterBinding.ensureInitialized();

  final configuration = DatadogConfiguration(
    clientToken: '<YOUR_DATADOG_CLIENT_TOKEN>',
    env: 'production', // or 'staging', 'development', etc.
    site: DatadogSite.us1, // Adjust to your Datadog region (us1, eu1, etc.)
    serviceName: 'your-flutter-app',
    trackingConsent: TrackingConsent.granted,
    nativeCrashReportEnabled: true,
    rumConfiguration: DatadogRumConfiguration(
      applicationId: '<YOUR_DATADOG_APPLICATION_ID>',
    ),
  );

  await DatadogSdk.runApp(configuration, () async {
    runApp(MyApp());
  });
}
```

Replace:
- `<YOUR_DATADOG_CLIENT_TOKEN>` with your Client Token.
- `<YOUR_DATADOG_APPLICATION_ID>` with your Application ID.

Refer to the [Datadog Flutter RUM documentation](https://docs.datadoghq.com/real_user_monitoring/flutter/) for further details.

---

## Step 4: Validate the Setup

Run your Flutter app:

```shell
flutter run
```

- Perform various interactions (navigation, clicks, etc.) within your app.
- Verify the integration in your [Datadog RUM dashboard](https://docs.datadoghq.com/real_user_monitoring/explorer/).

---

## Step 5: Advanced Configuration (Optional)

You can enhance your monitoring by adding:
- Custom attributes and user sessions
- Detailed error tracking and performance insights
- Network monitoring capabilities

For advanced setups, refer to [Datadog RUM Advanced Configuration documentation](https://docs.datadoghq.com/real_user_monitoring/guide/advanced_configuration/).

---

## Best Practices
- Apply consistent tags for environments (dev/stage/prod), application versions, and regions.
- Follow clear naming conventions for easy identification and filtering of data.

---

## Implementation Checklist
- [ ] Create Datadog RUM application, obtain Client Token and App ID.
- [ ] Install the Datadog Flutter plugin.
- [ ] Initialize Datadog RUM in your Flutter application.
- [ ] Confirm RUM data flow in the Datadog dashboard.
- [ ] Configure custom attributes and tags.
