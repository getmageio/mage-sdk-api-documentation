# Mage SDK API Documentation
[Mage](https://www.getmage.io/) is a price optimization tool for in-app purchases.<br>
Forget continuously setting up A/B-Tests or hiring data science teams for your app pricing! Mage fully automates simultaneous price testing & setting for in-app purchases in over 170 countries separately. So you don't have to deal with it!<br>
Get started with [Mage](https://www.getmage.io/) to scale your products worldwide!

This is a documentation of the Mage API used by the SDKs. In case you're interested in writing your own bridges feel free to do so or even better: publish your own open-source SDK.<br>
The API is documented according to the [openapi3.0](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.0.md) standard.

## Currently Supported Mage SDK's
- [iOS](https://github.com/getmageio/mage-ios-sdk)
- [Java](https://github.com/getmageio/mage-android-sdk)
- [React Native](https://github.com/getmageio/mage-react-native-sdk)

# Recommended Usage
The Mage SDK API is kept quite simple with just two calls. A more beautiful description can be found [here](https://hub.apitree.com/FlyingLasagna/privatecheck/). Sadly APITree does not show all openAPI3.0 definitions, so please have a look at this open source github repository for a complete documentation.

The servers URL is: `https://room-of-requirement.getmage.io`<br>
Current Version: 1.1.0<br>
Version Path: `/v1`<br>
There are two paths available: `/accio` and `/lumos`.<br>
(Yeah we like magic, don't you?)<br>

## Authorization
For authorization, you set your personal App-API-Key in your request header as `Token`.
You can access your personal App-API-Key in the Mage Web App.

## Receiving your optimal IAP pricing
We recommend retrieving the optimal in-app purchase (IAP) pricing right after your app started. That way, it can be ensured that there is no delay once you want to show your users the current prices. Watch out to take care of request timeouts or other failures. As a fallback, it proved to be best to define your default IAPs as an emergency IAP, which then will be used as a fallback and shown if something doesn't work as expected.<br>
To receive your IAP pricing, use the path `/accio`. The call `/accio` takes two arguments: `state` and `products`. Required is only the `state` argument, which contains the current users state. The current user state is used by Mage to determine the optimal price. The second argument `products` represents the list of product objects, which were received last time for this user. For devices connecting the first time to the mage backend, this attribute becomes irrelevant as obviously no products have been cached so far.
The response body contains a list of products. Which correlates to your products, you defined in your Mage Web App. But additionally, there has an `iapIdentifier` been set, which points to the IAP with the optimal price you added to your iTunes Connect or Google Play Console.<br>
The returned list of products should be cached for two reasons. First, every time you want to display an IAP to the user, you can retrieve the needed `iapIdentifier` from the list by the `productName`. Second, if you add this list to the call `/accio` at the next app start, Mage ensures that price changes won't happen too fast and too strong.

### Path
`https://room-of-requirement.getmage.io/v1/accio`

### Request Object
|Name     |Data Type    |Description    |
|---  |---  |---  |
|products       |array [[Product](#Product)]  |List of product objects, which were received last time for this user.    |
|state          |[State](#State)              |Information about the current users state.     |

### Response Object
|Name     |Data Type    |Description    |
|---  |---  |---  |
|message        |string                         |     |
|status         |long                           |     |
|products       |array [[Product](#Product)]    |     |

## Analyzing a new purchase with Mage
Each time your users make a purchase use `/lumos` to add this purchase to Mage so that the new purchase can help to calculate a new model. Therefore again, two arguments need to be passed: `state` and `product`. Both are required attributes. `state` contains also the current users state. `product` represents the bought product. It takes the product like the cached product.<br>
The response will contain a simple success message.

### Path
`https://room-of-requirement.getmage.io/v1/lumos`

### Request Object
|Name     |Data Type    |Description    |
|---  |---  |---  |
|product      |[Product](#Product)  |Currently bought product.    |
|state        |[State](#State)      |Information about the current users state.     |

### Response Object
|Name     |Data Type    |Description    |
|---  |---  |---  |
|message      |string     |     |
|status       |long       |     |

---

## Models
### <a name="State"></a>State
Information about the current users state.

|Name     |Data Type    |Description    |
|---  |---  |---  |
|appName                |string   |Your apps name.    |
|appUserId              |string   |Your user identifier for tracking the subscription status. This is important for subscriptions to track how long they run, if they convert from trial phases and so on. To notify Mage of subscription state changes please use the web API which can be found [here](#TODO).    |
|appVersion             |string   |Your current app version.    |
|buildNumber            |long     |Your current build number.     |
|bundleId               |string   |The bundle-Id of your app (it doesn't matter whether it's iOS or Android).    |
|countryCode            |string   |The users current country location. Use the [ISO 3166](https://www.iso.org/iso-3166-country-codes.html) standard! From iOS devices, you receive the alpha-2 code and from Android the alpha-3 code. So use depending on the device the standard accordingly.     |
|currencyCode           |string   |The currency code according to the [ISO 4217](https://www.iso.org/iso-4217-currency-codes.html) standard.     |
|deviceBrand            |string   |Manufacturer of the device your app is running on (e.g. 'Apple', 'google', 'Nokia', etc.).     |
|deviceId               |string   |A more specific identifier of the device model your app is running on (e.g. 'iPhone10,6', 'iPhone8,4', etc.).     |
|deviceModel            |string   |The Model of the device your app is running on (e.g. 'iPhone X', 'iPhone 11 Pro', etc.).     |
|deviceType             |string   |Which kind of device your app is running on (e.g. 'Handset' or 'Tablet').     |
|isEmulator             |boolean  |Let's the system know, whether you're currently just implementing Mage and running it within an emulator. Always set TRUE when implementing and using an emulator so that Mage won't learn your test purchases! But don't forget to turn it of, when you are running it in production!     |
|isProduction           |boolean  |Let's the system know, whether you're currently just implementing Mage or if it is running live. Always set FALSE when implementing, so Mage won't learn your test purchases!    |
|isStrict               |boolean  |A flag to let the SDK crash when errors occur. This way, you can test if you set up the SDK correctly.     |
|platform               |string   |Whether we're in the Apple App Store (use 'Apple') or in the Google Play Store (use 'Google').    |
|storeCode              |string   |The users' current store location. Use the [ISO 3166](https://www.iso.org/iso-3166-country-codes.html) standard! From iOS devices, you receive the alpha-3 code and from Android the alpha-2 code. So use depending on the device the standard accordingly.    |
|systemName             |string   |OS name (e.g. 'Android' or 'iOS').     |
|systemVersion          |string   |Current OS version, on which the app is running.     |
|time                   |long     |Current time (unix-timestamp).     |
|timeZone               |string   |Code of timezone.    |
|timeZoneCode           |string   |Code of timezone plus offset.    |

### <a name="Product"></a>Product
The Mage internal product for your different price levels.

|Name     |Data Type    |Description    |
|---  |---  |---  |
|countryId                |long     |Id of the country, in which your IAP is currently served.     |
|iapIdentifier            |string   |Identifier, used by apple/google, for your in-app purchase.    |
|id                       |long     |Internal IAP Id    |
|periodTime               |float    |For subscriptions only. Billing cycles for your subscription.    |
|periodTimeOfferLength    |float    |For subscriptions only. Duration of the reduced offer length.    |
|productGroupName         |string   |Name of the productgroup to which your product belongs.    |
|productId                |long     |Internal Product Id    |
|productName              |string   |Name of your product     |
|productType              |string   |Type of your product (Subscription, Consumable, Non-Consumable).     |
|recommendationId         |long     |Id of the recommendation, which suggested this price.    |
|trialTime                |float    |For subscriptions only. Duration of the trial time for your subscription.    |

### <a name="Response"></a>Response
|Name     |Data Type    |Description    |
|---  |---  |---  |
|message      |string     |     |
|status       |long       |     |

### <a name="ProductResponse"></a>ProductResponse
|Name     |Data Type    |Description    |
|---  |---  |---  |
|message        |string                         |     |
|status         |long                           |     |
|products       |array [[Product](#Product)]    |     |

---

## Version History
