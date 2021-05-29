
## 1.1) NHIF Member Verification

<br><br>

Verification of NHIF beneficiaries must be done by the health facility before issuing service. This is to ensure that patient receiving NHIF Service is a valid NHIF beneficiary according to NHIF regulations. Upon successful verification the Authorization number will be issued and needs to be availed to NHIF during claim submission (In electronic claim and/or claim form). This verification service can be called through the following URL:

<br>

https://verification.nhif.or.tz/nhifservice/breeze/verification/AuthorizeCard?CardNo= Your_Card_Number&VisitTypeID=YOUR_VISIT_TYPE&ReferralNo=YOUR_REFERRAL_NO&Remarks=AUTHORIZATION_REMARKS

<br>

Visit Type ID has the following possible values

- 1 = Normal Visit
- 2 = Emergency
- 3 = Referral
- 4 = Follow up Visit


<br>


NHIF beneficiary is allowed to visit one facility per day under normal visit. Under emergency visit, member may be successful verified even if member has already visited another facility in that particular day. 

<br>

For example, Patient visited Temeke Hospital in the morning under normal visit. Then in the evening, he/she comes under a situation where a need rises to see a doctor and goes to another hospital other than Temeke Hospital. Under this emergency, the patient may authorized as an Emergency Visit.

<br>

The third situation is when a beneficiary visits a facility and gets basic treatment but later it becomes apparent that the he/she must be referred to another facility. 

<br>

For example a beneficiary visited with Mwananyamala Hospital and later is referred to Muhimbili Hospital. Under this situation the first facility (Mwnanyamala Hospital) will refer the beneficiary to another facility (e.g Muhimbili Hospital) using a referral system as provided by NHIF service portal.

<br>

The fourth scenario arises when a patient who is already under treatment at a referral hospital need to come back for a follow up visit. Since he/she will not be accepted under normal visit, service provider may authorize him/her using a Follow up visit. 

<br>

In this case, a `referral number` issued earlier must be specified. When this action (function) is called from the `web service` the following results will be returned if the call runs successfully.

<br>

a) The following is the result when `card exists` and request is `accepted`:

```json
{
    "CardNo":"01-nhif241",
    "CardStatus":"Active",
    "FirstName":"Amour",
    "MiddleName":"R",
    "LastName":"Hamad",
    "FullName":"Amour R Hamad",
    "Gender":"Male",
    "DateOfBirth":"1974- 03 - 18",
    "ExpiryDate":"",
    "AuthorizationStatus":"ACCEPTED",
    "AuthorizationNo":"720002",
    "EmployerNo":"7000885",
    "SchemeID":" 1001 ",
    "ProductCode":"NH001",
    "Remarks":"Verified OK"
}
```

<br>

b) The following is the result when `card exists` but card is `inactive` or `rejected` by any other reason:



```json
{
    "CardNo":"02-nhif241",
    "CardStatus":"Inactive",
    "FirstName":"Saada",
    "MiddleName":"M",
    "LastName":"Khamis",
    "FullName":"Saada M Khamis",
    "Gender":"Female",
    "DateOfBirth":"1983- 06 - 13",
    "ExpiryDate":"",
    "AuthorizationStatus":"REJECTED",
    "AuthorizationNo":"",
    "EmployerNo":"7000885",
    "SchemeID":" 1001 ",
    "ProductCode":"NH001",
    "Remarks":"Card Missing"
}
```


<br>

c) The result when the card `does not exist` in the NHIF database but due to the reasons known to NHIF the beneficiary has been `allowed` service with a warning to submit his/her card to the NHIF office for verification:

```json
{
    "CardNo":"01-900035308",
    "FirstName":"",
    "MiddleName":"",
    "LastName":"",
    "FullName":"",
    "Gender":"",
    "DateOfBirth":"",
    "ExpirtDate":"",
    "CardStatus":"Unknown",
    "AuthorizationStatus":"ACCEPTED",
    "AuthorizationNo":"730003",
    "EmployerNo":"",
    "SchemeID":"",
    "ProductCode":"NH001",
    "Remarks":"The beneficiary should submit the card to the nearby NHIF for verification otherwise the card will be blocked and no service will be issued"
}
```

<br>




d) When the patient in c returns to the facility to get service without verifying his/her card at NHIF:


```json
{
    "CardNo":"01-900035308",
    "FirstName":"",
    "MiddleName":"",
    "LastName":"",
    "FullName":"",
    "Gender":"",
    "DateOfBirth":"",
    "ExpiryDate":"",
    "CardStatus":"Invalid",
    "AuthorizationStatus":"Rejected",
    "AuthorizationNo":"",
    "EmployerNo":"",
    "SchemeID":"",
    "ProductCode":"NH001",
    "Remarks":"The beneficiary was required to verify his/her card at NHIF office"
}
```

<br>

e) The result when the card number entered is not a valid card number that if an old card number does not comply with with the NHIF format or if the card number is in in new number format does not exists in the database.


```json
{
    "CardNo":"07-nhif241",
    "FirstName":"",
    "MiddleName":"",
    "LastName":"",
    "FullName":"",
    "Gender":"",
    "DateOfBirth":"",
    "ExpirtDate":"",
    "CardStatus":"Invalid",
    "AuthorizationStatus":"REJECTED",
    "AuthorizationNo":"",
    "EmployerNo":"",
    "SchemeID":" 1001 ",
    "ProductCode":"NH001",
    "Remarks":"The card number entered is invalid. Please check it again or the beneficiary should visit the nearest NHIF office for verification."
}
```

<br>

The old NHIF card number has a format `0-[1-6]-[\w]` for regular expression
or `0[1-6]-%` for SQL e.g. 01-890765644. 

The new NHIF card number has a format `[1-5][01-99][\d]`. 


The length of card number is 12 digits and does not contain alphanumeric characters. eg. 308900035308. 

<br>


Most important thing in the result is the `AuthorizationStatus` whether `"ACCEPTED"` or `"REJECTED"`. 

That determines whether the beneficiary should receive service or not. To connect to the API use `TOKEN` authentication.


`Username` and `Passwords` will be provided by NHIF upon request. To get card details make an authenticated call as follows. The result is a JSON that contain card details.

<br>

https://verification.nhif.or.tz/nhifservice/breeze/verification/GetCardDetails?CardNo=Your_Card_Number

<br>


To be Get Authentication Token the request will be sent to: https://verification.nhif.or.tz/nhifservice/Token when user name and passwords will be provided through a POST`.

The snipped below shows how to get Authorization header with PHP that can
later be put on the request.

**Assume you an include file that has a constant TOKEN_END_POINT defined**

```php

<?php

define('TOKEN_END_POINT','https://verification.nhif.or.tz/nhifservice/Token');
define('SERVICE_END_POINT','https://verification.nhif.or.tz/nhifservice/breeze/');



public function getAuthenticationHeader($username, $password) {

    // Construct the body for the STS request
    $authenticationRequestBody = 'grant_type=password&username='.$username.'&password='.$password;

    // Using curl to post the information to STS and get back the authentication response
    $ch = curl_init();

    // set url
    curl_setopt($ch, CURLOPT_URL, TOKEN_END_POINT);

    // Get the response back as a string
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, TRUE);

    // Mark as Post request
    curl_setopt($ch, CURLOPT_POST, 1 );

    // Set the parameters for the request
    curl_setopt($ch, CURLOPT_POSTFIELDS, $authenticationRequestBody);

    // By default, HTTPS does not work with curl.
    curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);

    // read the output from the post request
    $output = curl_exec($ch);

    // close curl resource to free up system resources
    curl_close($ch);

    // decode the response from sts using json decoder
    $tokenOutput = json_decode($output);

    return 'Authorization:'. $tokenOutput->{'token_type'}. ' '. $tokenOutput->{'access_token'};

}

```
