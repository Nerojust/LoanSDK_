[ ![Download](https://api.bintray.com/packages/kingsmentor/maven/eligibility/images/download.svg) ](https://bintray.com/kingsmentor/maven/eligibility/_latestVersion)

# Loan-SDK
Android SDK for Loan Apps

## Gradle Dependency

Add this to your module's `build.gradle` file:

```gradle
dependencies {

  implementation 'io.intelia.anyi:eligibility:2.1.2'
}
```

---

## The Basics

**Kindly Note**, initialize LoanEligibility SDK:

To initialise LoanEligibility SDK, call : `LoanEligibility.init(context, YOUR_API_KEY)`

this will either : 

`throw an exception` when permission is not granted to read sms (so ensure your application have been granted the proper permission before making this call)

```java
throw Exception("android.Manifest.permission.READ_SMS permission is required")
```

or  `return QueryImpl`

### QueryImpl

QueryImpl allows you to perform the following operation with the SDK :


* `fun calculateEligibility(): Observable<Eligibility>`

this calculates loan eligibility for user. 

```kotlin
    fun calculateEligibility() {
        queryImpl.calculateEligibility()
            .doOnError {
                eligibility.postValue(null)
            }
            .onErrorResumeNext(io.reactivex.Observable.empty())
            .subscribeOn(Schedulers.io())
            .observeOn(AndroidSchedulers.mainThread())
            .subscribe {
                eligibility.postValue(it)
            }
    }
```

Eligibility contains information duduced from User information contained in the device :

```kotlin
open class Eligibility(
    val eligible: Boolean = false,
    @SerializedName("expense-rate") val expense_rate: Double = 0.0,
    @SerializedName("hold-money-rate") val hold_money_rate: Double = 0.0,
    @SerializedName("risk-profile") val risk_profile: Double = 0.0,
    val status: Int = 0
)
```

* `fun smsData(): Observable<MutableList<SmsDataPoint>>`

this fetches finiancial related sms that can be used to make prediction about user's finiancial habit

```kotlin
usecase.smsData()
            .subscribeOn(Schedulers.io())
            .observeOn(AndroidSchedulers.mainThread())
            .subscribe {
                smsDataPoint.postValue(it)
            }
```
this returns `data class SmsDataPoint(val category: String, var sms: MutableList<Sms>)`

`category` signifies the category of this transaction.

` var sms: MutableList<Sms>` shows sms under this category

* `fun relevantApp(): RelevantApps`

this returns list of finaincial apps activiely being used by the users. 


* `fun relevantApp(): RelevantApps`

this returns list of finaincial apps activiely being used by the users. 

* `fun isRelevantSms(sms: String): Boolean`

verifies if the string content is transactional. This is useful when intercepting user sms. New transactional sms can triger a new check for user eligibility. 

```kotlin 
  if(queryImpl.isRelevantSms(sms))
      calculateEligibility()
 ```

* `fun isRelevantApp(packageName: String): Boolean`

verifies if a packagename should affect user's eligibility calculation. This largely depends on if the user activiely use the application. 
Otherwise, it returns false immediately. This is particularly useful when your application listens to new app installation. 

```kotlin 
  if(queryImpl.isRelevantApp(packageName))
      calculateEligibility()
 ```
 
### Testing the SDK

You will find a basic text application [here](https://github.com/intelia/Loan-SDK/tree/master/app) .

Kindly go through the implementation, build and run on your emulator or physical devices.

The test app test the basic functionality of the sdk. So, don't restrict your usage or implementation to what is found therein. 

## Using in Cordova 

first add the cordova plugin : 

`cordova plugin add cordova-plugin-intelia-loaneligibility`

## The Basics

**Kindly Note**, initialize LoanEligibilty SDK:

To initialise LoanEligibility SDK, call :

`window.plugins.loaneligibility.init('key','name',{}, function(a){alert('toast success: ' + a)}, function(b){alert('toast error: ' + b)})`

this will either : 

```java
throw Exception("android.Manifest.permission.READ_SMS permission is required")
```

or  `return QueryImpl`

You can also ensure SMS permission is granted before making call to the SDK. 
If the first step was completed without any error. These other calls will run successfully. 

## Fetch data

`window.plugins.loaneligibility.data({}, function(a){alert('toast success: ' + a)}, function(b){alert('toast error: ' + b)})`

## Calculate Eligibility
`window.plugins.loaneligibility.calculate({}, function(a){alert('toast success: ' + a)}, function(b){alert('toast error: ' + b)})`

Both calls returns JSON representation of `Eligibility` Object (which is updated overtime)


Eligibility contains information deduced from User information contained in the device :

```kotlin
open class Eligibility(
    val eligible: Boolean = false,
    @SerializedName("expense-rate") val expense_rate: Double = 0.0,
    @SerializedName("hold-money-rate") val hold_money_rate: Double = 0.0,
    @SerializedName("risk-profile") val risk_profile: Double = 0.0,
    val status: Int = 0
)
```
   

