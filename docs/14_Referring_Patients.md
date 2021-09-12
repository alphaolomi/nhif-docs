# 1.4) Referring Patients


NHIF protocols require its member to visit a single facility for one
particular day where he/she will be verified and issued an Authorization
number. In case the first facility needs to refer the patient to another
facility for any reason the fund enables the reference process through the
Service portal and for facilities that do system to system integration the
Fund provides an API to enable this. The following are important details
for integrators:


Token Endpoint: https://verification.nhif.or.tz/nhifservice/Token


URL to send Referral request https://verification.nhif.or.tz/nhifservice/breeze/verification/AddReferral

A sample JSON submitted to the API

```json
{
  "CardNo": "01-NHIF45",
  "AuthorizationNo": "623456789",
  "PatientFullName": "Said Juma",
  "PhysicianMobileNo": "0655232365",
  "Gender": "Male",
  "PhysicianName": "Amour Rashid",
  "PhysicianQualificationID": 2,
  "ServiceIssuingFacilityCode": "01099",
  "ReferringDiagnosis": "084,085",
  "ReasonsForReferral": "Fractured hand needs attention from Orthopaedician at MOI"
}

```



A Response returned from the API

```json
{
  "$id": "1",
  "$type": "NHIFService.Models.PatientReferral, NHIFService",
  "ReferralID": "838ba4f0-8a80- 4658 - bba5-e61ff602ca1d",
  "CardNo": "01-NHIF45",
  "AuthorizationNo": "623456789",
  "PatientFullName": "Said Juma",
  "Gender": "Male",
  "ReferringDate": "2018- 01 - 29T10:27:31.637",
  "PhysicianName": "Amour Rashid",
  "PhysicianMobileNo": null,
  "ReferralNo": "21081000019",
  "ServiceIssuingFacilityCode": "01099",
  "SourceFacilityCode": "06697",
  "PhysicianQualificationID": 2,
  "ReferringDiagnosis": "084,085",
  "ReasonsForReferral": "Fractured hand needs attention from Orthopaedician at MOI",
  "CreatedBy": "arashid",
  "DateCreated": "2018- 01 - 29T10:27:31.637",
  "LastModifiedBy": "arashid",
  "LastModified": "2018- 01 - 29T10:27:31.637"
}

```




All the endpoints use `POST` Method. 


The following are possible values of `PhysicianQualificationID` and `FacilityCode`.



|ID| Qualification|
|--|--|
|1 | Super Specialist|
|2 | Specialist|
|3 | Medical Officer(MD)/Dental Surgeon(DDS)|
|4 | Assistant Medical Officer(AMO)/Assistant Dental Officer(ADO)|
|5 | Clinical Officer/Dental Assistant|
|6 | Assistant Clinical Officer|
|7 | Others|



|Facility Code |Facility Name|
|--|--|
|01099 |  Muhimbili National Hospital|
|01100 |  Muhimbili Orthopaedic Institute|
|05447 |  Muhimbili Ocean Road Clinic|
|06936 |  JAKAYA KIKWETE CARDIAC INSTITUTE-MUHIMBILI|
|07229 |  Jakaya Kikwete Cardiac Clinic|
|05004 |  Ocean Road Cancer Institute|
|01334 |  Kibong'oto Special Hospital|
|00947 |  Mirembe Mental Health Hospital|



!!! hint
    Just contact NHIF to get to know your `FacilityCode`