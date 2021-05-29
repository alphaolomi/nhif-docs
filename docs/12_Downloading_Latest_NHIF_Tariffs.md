# 1.2) Downloading Latest NHIF Tariffs

<br><br>

The price package for each facility can be obtained using the API Call specifying the facility Code as shown below:


## (i) **NHIF Price List Without Excluded Service**

A successful Calling of the url provided below will give a JSON Document that will have the price package for a specified Facility Code. In this result, the PackageID has a very great significance.

<br>
https://verification.nhif.or.tz/claimsserver/api/v1/Packages/GetPricePa
ckage?FacilityCode=Your_Facility_Code


Example https://verification.nhif.or.tz/claimsserver/api/v1/Packages/GetPricePackage?FacilityCode=

<br>
This will give the following result:


```json
{
   [
    {
        "ItemCode": "10001",
        "ItemName": "General Practitioner Consultation",
        "PackageID": 102,
        "UnitPrice": 10000.0,
        "IsRestricted": false
    },
    {
        "ItemCode": "10001",
        "ItemName": "General Practitioner Consultation",
        "PackageID": 201,
        "UnitPrice": 25000.0,
        "IsRestricted": false
    }
   ]
}
```


<br><br>

### Description:

The information provided in the JSON Document contains the following important fields for integrators:

|FieldName | Description| Data Type|
| -- | -- | -- |
|ItemCode |A unique code for each service issued by NHIF. |Text|
|ItemName Service Name |Text|
|PackageID ID of the price package. NHIF maintain different packages |Integer|
|UnitPrice| Price for a single unit of Item or Service |Decimal (18,2)|
|IsRestricted | A flag showing whether the service has restriction or not. If a service has restriction the beneficiary must get approval from NHIF before he/she can access that service. |Boolean|



## (ii) **NHIF Price List With Excluded Service**

To get NHIF price list with excluded service, please call the url provided below:

<br>

https://verification.nhif.or.tz/claimsserver/api/v1/Packages/GetPricePackageWithExcludedServices?FacilityCode=Your_Facility_Code

Example: https://verification.nhif.or.tz/claimsserver/api/v1/Packages/GetPricePackageWithExcludedServices?FacilityCode=01099


<br>
This will give the following result:

```json
{
  "FacilityCode": " 01099 ",
  "PricePackage": [
    {
      "ItemCode": "10001",
      "ItemName": "General Practitioner Consultation",
      "PackageID": 102,
      "SchemeID": 1001,
      "UnitPrice": 10000.0,
      "IsRestricted": false
    },
    {
      "ItemCode": "10001",
      "ItemName": "General Practitioner Consultation",
      " PackageID": 201,
      "SchemeID": 1002,
      "UnitPrice": 25000.0,
      "IsRestricted": false
    }
  ],
  "ExcludedServices": [
    {
      "ItemCode": "10001",
      "SchemeID": 1001,
      "ExcludedForProducts": "NH003,NH004"
    },
    {
      "ItemCode": "10002",
      "SchemeID": 1003,
      "ExcludedForProducts": "NH001,NH002"
    }
  ]
}
```

<br>

### **Description:**

The information provided in the JSON Document contains the following important fields for integrators:

**PricePackage**


|FieldName |Description | Data Type|
| -- | -- | -- |
|ItemCode |A unique code for each service issued by NHIF. |Text|
|ItemName| Service Name Text SchemeID ID of the scheme subscribed by NHIF member |Integer|
|PackageID| ID of the price package. NHIF maintain different packages for different schemes |Integer|
|UnitPrice| Price for a single unit of Item or Service| Decimal (18,2)|
| IsRestricted | A flag showing whether the service has restriction or not. If a service has restriction the  beneficiary must get approval from NHIF before he/she can access that service. |Boolean|


<br>
**Excluded Services**


|FieldName| Description| Data Type |
| -- | -- | -- |
|ItemCode |A unique code for each service issued by NHIF. |Text|
|SchemeID |ID of the scheme subscribed by NHIF member |Integer|
|ExcludedForProducts| List of ProductCode separated by comma |Text|



**Note:**


NHIF member authorized above is eligible to service enlisted only in the Price Package under his/her schemeID provided that this `schemeID` and `ProductCode` are not found in the list of Excluded Services for that particular service.
