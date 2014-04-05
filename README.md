<!---
 license: Licensed to the Apache Software Foundation (ASF) under one
         or more contributor license agreements.  See the NOTICE file
         distributed with this work for additional information
         regarding copyright ownership.  The ASF licenses this file
         to you under the Apache License, Version 2.0 (the
         "License"); you may not use this file except in compliance
         with the License.  You may obtain a copy of the License at

           http://www.apache.org/licenses/LICENSE-2.0

         Unless required by applicable law or agreed to in writing,
         software distributed under the License is distributed on an
         "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
         KIND, either express or implied.  See the License for the
         specific language governing permissions and limitations
         under the License.
-->


## ![iBeacon Cordova Plugin](http://icons.iconarchive.com/icons/artua/mac/128/Bluetooth-icon.png) Cordova / Phonegap iBeacon plugin

### Features

 * Ranging
 * Monitoring
 * Advertising as iBeacon (only works in foreground mode)

### Installation

```
cordova plugin add https://github.com/petermetz/cordova-plugin-ibeacon.git
```

### Usage

The plugin's API closely mimics the one exposed through the [CLLocationManager](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html) in iOS 7. There is some added sugar as well, like the ability to interact with multiple iBeacons through a single call.

#### Standard [CLLocationManager](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html) functions

##### Creating CLBeaconRegion DTOs
```
/**
 * Function that creates a CLBeaconRegion data transfer object.
 * 
 * @throws Error if the CLBeaconRegion cannot be created.
 */
function createBeacon() {
   var identifier = 'beaconAtTheMacBooks'; // optional
   var major = 1111; // mandatory
   var minor = 2222; // mandatory
   var uuid = '550e8400-e29b-41d4-a716-446655440000'; // mandatory

   // throws an error if the parameters are not valid
   var beacon = new IBeacon.CLBeaconRegion(uuid, major, minor, identifier);
   return beacon;   
} 
```
 
##### Start monitoring a single iBeacon
```

var onDidDetermineStateCallback = function (result) {
     console.log(result.state);
};

var beacon = createBeacon();
IBeacon.startMonitoringForRegion(beacon, onDidDetermineStateCallback);

```
 

##### Stop monitoring a single iBeacon
```

var beacon = createBeacon();
IBeacon.stopMonitoringForRegion(beacon);

```
 
 
##### Start ranging a single iBeacon
```

var onDidRangeBeacons = function (result) {
   console.log('onDidRangeBeacons() ', result);
};

var beacon = createBeacon();
IBeacon.startRangingBeaconsInRegion(beacon, onDidRangeBeacons);


```
 
##### Stop ranging a single iBeacon
```

var beacon = createBeacon();
IBeacon.stopRangingBeaconsInRegion(beacon);

```

##### Determine if advertising is turned on.

```
IBeacon.isAdvertising(function(pluginResult) {
    var isAdvertising = pluginResult.isAdvertising;
    console.log('isAdvertising:' + isAdvertising);
});
```

##### Start advertising device as an iBeacon
```
var beacon = createBeacon();
var onPeripheralManagerDidStartAdvertising = function(pluginResult) {
    console.log('onPeripheralManagerDidStartAdvertising() pluginResult: ', pluginResult);
}

IBeacon.startAdvertising(beacon, onPeripheralManagerDidStartAdvertising);

```

##### Stopping the advertising
```
IBeacon.stopAdvertising(); // optionally you can specify a success callback as the first parameter
```

#### Convenience methods

##### Handle multiple beacons with the same call:
```

var beacon1 = createBeacon(); 
var beacon2 = createBeacon(); 
var beacon3 = createBeacon(); 
var beacons = [beacon1, beacon2, beacon3]; 

IBeacon.startMonitoringForRegions(beacons); 
IBeacon.startRangingBeaconsInRegions(beacons);
```


## Contributions

> Contributions are welcome at all times, please make sure that the tests are running without errors
> before submitting a pull request.

### How to execute the tests - OS X

#### Prerequisites Of The Test Runner
* [Dart SDK](http://dartlang.org) installed on the path (Minimum version: 1.2)
* [NodeJS](http://nodejs.org/)
* [NPM](https://www.npmjs.org/)
* [Cordova NPM package](https://www.npmjs.org/package/cordova) (Minimum version: 3.4.0-0.1.3)
* [XCode](https://developer.apple.com/xcode/) (Tested with 5.0.2)


```
dart test/run_tests.dart
```

Executing the test runner will do the following:
* Generates a Cordova project
* Add the iOS platform
* Installs the iBeacon plugin from the local file-system.
* Launches XCode by opening the project.

### How to execute the tests - Non OS X

* Open an app which has Cordova iBeacon plugin installed in XCode
* Install it onto a device or simulator
* Open Safari
* Go to the dev tools window
* Paste the code below (the contents of ```test/test_www_assets/js/tests.js```) into the Javascript console
* Run the snippet, there should not be any errors.

```
try {
  function createBeacon(index) {
    var addition = parseInt(index);
    addition = isFinite(addition) ? addition : 0;
    var identifier = 'cordova-ibeacon-plugin-test'; // optional
    var major = 1111; // mandatory
    var minor = 1111 + addition; // mandatory
    var uuid = '11111111-1111-1111-1111-111111111111'; // mandatory

    // throws an error if the parameters are not valid
    var beacon = new IBeacon.CLBeaconRegion(uuid, major, minor, identifier);
    return beacon;
  }

  var b1 = createBeacon();
  var b2 = createBeacon();
  var b3 = createBeacon();

  var arrayOfBeacons = [b1, b2, b3];

  var onDidDetermineStateCallback = function(result) {
    console.log(result.state);
  };

  var beacon = createBeacon();
 IBeacon.startMonitoringForRegion(beacon, onDidDetermineStateCallback);
 IBeacon.stopMonitoringForRegion(beacon);

 IBeacon.startMonitoringForRegions(arrayOfBeacons, onDidDetermineStateCallback);
 IBeacon.stopMonitoringForRegions(arrayOfBeacons);

 var onDidRangeBeacons = function(result) {
   console.log('onDidRangeBeacons() ', result);
 };
 IBeacon.startRangingBeaconsInRegion(beacon, onDidRangeBeacons);
 IBeacon.stopRangingBeaconsInRegion(beacon);

 IBeacon.startRangingBeaconsInRegions(arrayOfBeacons, onDidRangeBeacons);
 IBeacon.stopRangingBeaconsInRegions(arrayOfBeacons);

  var onPeripheralManagerDidStartAdvertising = function(pluginResult) {
    console.log('onPeripheralManagerDidStartAdvertising() pluginResult: ', pluginResult);
  }
  IBeacon.startAdvertising(createBeacon(), onPeripheralManagerDidStartAdvertising);

  if (app && app.receivedEvent) {
    app.receivedEvent('deviceready');  
  } else {
    alert('Tests were successful.');
  }
  

} catch (error) {
  alert('There were test failures. \n' + error.message);
}

```