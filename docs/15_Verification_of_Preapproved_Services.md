
# 1.5) Verification of Pre-approved Services

There are some services that need to be pre-approved where a beneficiary is given a special letter that introduces him/her to the service provider. To make sure that health facilities provide services to only genuine beneficiaries the Fund provides an API where they can verify the authenticity of approval letters presented by the beneficiaries. The URL for this service is as shown below:


```
https://verification.nhif.or.tz/nhifservice/breeze/verification/GetReferenceNoStatus?CardNo=YOUR_CARD_NO&ReferenceNo=YOUR_REFERENCE_NO&ItemCode=YOUR_ITEM_CODE
```


|Parameter| Name Details|
|--|--|
|CardNo| The beneficiary Card Number as printed on the beneficiary card|
|ReferenceNo| Approval reference number as presented on the approval letter presented by the beneficiary|
|ItemCode| NHIF Service code as defined in the NHIF service list|



The result of this request will be a json containing only one value for Status. This can be either VALID or INVALID.


