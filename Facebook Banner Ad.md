## Showing facebook banner add in flutter

> Write flutter code to show facebook ads as a banner

---

To show Facebook ads as a banner in your Flutter app, you'll first need to set up the Facebook Audience Network SDK by following the instructions in the Facebook documentation. Once you have the SDK integrated, you can use the following code to show the banner ad:

```dart
import 'package:flutter/material.dart';
import 'package:facebook_audience_network/facebook_audience_network.dart';

class MyHomePage extends StatefulWidget {
  const MyHomePage({Key? key}) : super(key: key);

  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  bool _isInterstitialAdLoaded = false;

  @override
  void initState() {
    super.initState();
    // Load the banner ad
    FacebookAudienceNetwork.init();
  }

  @override
  void dispose() {
    // Dispose of the banner ad
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Facebook Banner Ad'),
      ),
      body: Center(
        child: FacebookBannerAd(
          placementId: 'YOUR_PLACEMENT_ID',
          bannerSize: BannerSize.STANDARD,
          listener: (result, value) {
            print('Banner Ad: $result --> $value');
          },
        ),
      ),
    );
  }
}
```

Replace `'YOUR_PLACEMENT_ID'` with the actual placement ID for your Facebook banner ad. The `BannerSize` enum provides different banner ad sizes you can use.

Note that you need to initialize the Facebook Audience Network SDK before loading the banner ad, which is done in the `initState` method of the stateful widget. The `FacebookBannerAd` widget takes care of loading and displaying the banner ad, and provides a listener callback that you can use to track ad events.

---

> How to get placment id

---

To get the placement ID for your Facebook ad, you'll need to create a new placement in the Facebook Audience Network dashboard. Here's how:

1.  Go to the Facebook Audience Network dashboard: [https://www.facebook.com/audiencenetwork/](https://www.facebook.com/audiencenetwork/)
2.  Click on the "Monetize" tab in the top menu.
3.  Click on "Create Ad Placement" and select the type of placement you want to create (in this case, a banner ad).
4.  Fill in the details for your placement, including the app and ad format.
5.  Click "Create" to create the placement.
6.  Once the placement is created, you'll be given a placement ID. Copy this ID and use it in your Flutter app code to load the ad.

Note that you'll need to have a Facebook developer account and your app must be registered with Facebook in order to create an ad placement.

---
