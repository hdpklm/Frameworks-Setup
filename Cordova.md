## Create progect
```sh
# cordova create path [id [name]] [options]
cordova create test-project com.example.hello HelloWorld
cd test-project
cordova platform add android@9.1.0
```

# plugins

## list
```sh
cordova plugin list
```

## add plugins
```sh
cordova plugin add [plugin-name]
```

## remove plugins
```sh
cordova plugin rm [plugin-name]
```

# Others

## admob: for google ads mobile
> https://www.npmjs.com/package/cordova-admob
```sh
cordova plugin add cordova-admob
```
> obtain api-key from google admob url: https://apps.admob.com/

> add this in index.js in function onDeviceReady

```js
	// Set AdMobAds options:
	admob.setOptions({
		publisherId: "api-key",  // Required
		// interstitialAdId: "ca-app-pub-XXXXXXXXXXXXXXXX/IIIIIIIIII",  // Optional
		autoShowBanner: true,                                      // Optional
		autoShowRInterstitial: false,                                     // Optional
		autoShowRewarded: false,                                     // Optional
		// tappxIdiOS: "/XXXXXXXXX/Pub-XXXX-iOS-IIII",            // Optional
		// tappxIdAndroid: "/XXXXXXXXX/Pub-XXXX-Android-AAAA",        // Optional
		tappxShare: 0.5                                        // Optional
	});

	// Start showing banners (atomatic when autoShowBanner is set to true)
	admob.createBannerView();

	// Request interstitial ad (will present automatically when autoShowInterstitial is set to true)
	admob.requestInterstitialAd();

	// Request rewarded ad (will present automatically when autoShowRewarded is set to true)
	admob.requestRewardedAd();
```