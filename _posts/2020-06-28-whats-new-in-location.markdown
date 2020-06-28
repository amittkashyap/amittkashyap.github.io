---
layout: single
title:  "What's new in Location!"
date:   2020-06-28 10:49:00 +0530
categories: ["WWDC20"]
tags: ["iOS", "CoreLocation", "Swift", "WWDC20", "iOS14"]
---
WWDC20 has added major features in Location for the sake of user's privacy. In this post I'll try to explain what has changed for good and what's new :point_down:

I'm sure some of us have asked this question at some point: Does the app needs to know my exact location? 

Most often, the answer is *no* :no_mouth:

> So Apple has come up with a new option “Precise” for accuray, when app asks for location permission!

![Precise Option](/assets/images/whats-new-in-location.png)

-  You can access this option in **Settings** of your app
-  Also, apps **cannot opt out** of this feature
-  Users control the authorization

...so you'r thinking  how do we use the new Accuracy API, well here goes the new enum type :boom:

```
enum CLAccuracyAuthorization { 
  case fullAccuracy
  case reducedAccuracy
}
```
Additionally in iOS 14: 

- the `authorizationStatus() class func` method will be deprecated and instead it will be a `property`

- Also, deprecating the old delegate method as pointed here :point_down: 

![New API](/assets/images/callback.png)

The new delegate method will be called whenever the user change the *authorization status or accuracy*. This means you can handle both inside the delegate.

```
  var gettingExactLocation: Bool = false
  var gotSomeKindOfPermission: Bool = false
  func locationManagerDidChangeAuthorization(_ manager: CLLocationManager) {
  
    switch manager.authorizationStatus {
    case .authorizedAlways, .authorizedWhenInUse:
      gotSomeKindOfPermission = true
    case .denied, .notDetermined, .restricted:
      gotSomeKindOfPermission = false
    }

    switch manager.accuracyAuthorization {
    case .reducedAccuracy:
      gettingExactLocation = false
    case .fullAccuracy:
      gettingExactLocation = true
    }
  }
```

> Now let's talk about how object `CLLocation` will be delivered with `reducedAccuracy`

- Firstly, it will be recomputed only *4 times per hour*
- `didUpdateLocations` method will recive the object with a *center point* and a large *horizontalAccuracy*

> What might impact your app with `Precise` mode on? :worried:

- Backgorund behaviour will be changed in *reducedAccuracy*
- Region motnitoring also won't work with *reducedAccuracy* 

I know region monitoring plays an important role in many reminder apps, so

> What your app should do to adapt to the new `Precise` mode? :confused: 

- Your app can ask for *temporary permission* to full accuracy with a `purpose key` which will be defined in the `Info.plist` file of your app. The purpose key justifies *why your app needs temporary full accuracy*? Also, you'll have full accuracy for the rest of the session
- The less desirable way to get rid of most of your problems is still the same, which is to navigate users to Settings app and ask them to grant full accuracy permanently :wink: 

Even though *full accuracy* access permanently for your app is easy way out but Apple recommends using *reducedAccuracy* by default for privacy of the user

*Thanks for reading. I'll follow this up with an implementation in the upcoming article*.
