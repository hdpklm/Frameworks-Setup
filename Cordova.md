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
```sh
cordova plugin add cordova-admob
```
```js
// add this in index.js in function onDeviceReady
// obtain api-key from google admob url: https://apps.admob.com/
// and replacy in this object
admob.createBannerView({publisherId: "api-key"});
```