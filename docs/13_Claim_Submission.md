# 1.3) Claim Submission



Claim can be submitted online to NHIF by creating a Jason payload. This
payload can contain one or more folios (At least one folio) and associated
details. So it will always be a collection of folio objects even if only one folio
is to be submitted. The sample JSON data to be submitted can be provided
by the Fund upon request and the information in the JSON payload is as
follows:


### (i) **Folio**


|FieldName | Description| Data Type| Example|
| -- | --  | -- | -- |
|FolioID| Primary Key for a folio of visit |GUID| 817d6e75-3bef-4d25-a2e1-a3bc009530ab|
|FacilityCode | Unique identification number issued by NHIF to a particular health facility as part of the registration process |Text | 01099 |
|ClaimYear |Claim’s calendar year int 2020 ClaimMonth Month of claim Int 12 FolioNo A unique sequential number |Int |1|
|SerialNo | A Serial number as provided in NHIF 2A&B form |Text | 01099\ 2\2020\00001 |
|CardNo |Number of NHIF Beneficiary’s Cards | Text | 109900035308|
|FirstName| Card holder’s First Name |Text| Amour|
|LastName| Card holder’s Last Name |Text | Rashid|
|Gender | Card Holder’s Gender |Text| Male|
|DateOfBirth| Card Holder’s Date Of Birth |Date| 18/03/2020|
|TelephoneNo| |Text| 0686155255|
|PatientFileNo| Patient’s File number as recorded by the Hospital or other health facility |Text |MNH/03/99999|
|PatientFile | A PDF of the patient treatment details serialized as Base 64 string| |Text | GQ8XQAYFAiEMfN0qD0COTgMX......|
|AuthorizationNo |Authorization number issued during member verification |Text| 187870908768|
|AttendanceDate |The date that the patient attended for service |Date | 07/03/2020 |
|PatientTypeCode |IN PATIENT or OUTPATIENT| Text |OUT or IN|
|DateAdmitted| If patient was admitted then the admission date otherwise `null` |Date| 07/03/2020|
|DateDischarged |If patient was admitted then the discharge date otherwise `null` |Date| 08/03/2020|
|PractitionerNo |The Registration number of the medical practioner attending the patient as registered by the Medical council of Tanganyika |Text |2999999|
|CreatedBy| User who Created the Entry in the HMIS| Text| ghaule
|DateCreated |The Date the entry was created |Date| 07/03/2020|



###  (ii) **FolioDisease**


|FieldName | Description| Data Type| Example|
| -- | --  | -- | -- |
|FolioDiseaseID| Primary Key for a folio of visit |GUID| e9429e1c-f892-40ae-8c0a-a3bc0095681f|
|FolioID| The ID of the folio associated to this diese |GUIN | 817d6e75-3bef-4d25-a2e1-a3bc009530ab|
|DiseaseCode | Disease code as identified by WHO(Currently ICD 9) |Text|084|
|CreatedBy| User who Created the Entry in the HMIS| |Text |ghaule|
|DateCreated |The Date the entry was created |Date| 07/03/|




###  (iii) **FolioItem**


|FieldName | Description| Data Type| Example|
| -- | --  | -- | -- |
|FolioItemID| Primary Key for a service or item issued to the patient. This could be a medicine,procedure,s urgery,or consumables|GUID| e9429e1c-f892-40ae-8c0a-a3bc0095681f|
|FolioID| The ID of the folio associated to this Item or Service Issued |GUID| 817d6e75-3bef-4d25-a2e1-a3bc009530abItemCode |
|ItemCode | used to identify each Item or Service in NHIF Database |Text| 5036|
|ItemQuantity| Number of Items Claimed |Int| 10|
|UnitPrice |Price for each Item |Decimal |1500.|
|AmountClaimed |Sub Total (ItemQuantity*UnitPrice)\ |Decimal |15000.|
|ApprovalRefNo| If the service requires Appeoval then supply the Aproval reference number issued to the patient to get this ervice. |Text NHIF/REF/ |1000024|
|CreatedBy| User who Created the Entry in the HMIS |Text| ghaule|
|DateCreated| The Date the entry was created |Date | 07/03/2017|






The method that accepts json array that will contain one or more folios
and uses POST method which can be access via the following url:

https://verification.nhif.or.tz/claimsserver/api/v1/Claims/SubmitFolios

The authentication used is a Token Authentication (Bearer Token). An
Example is given in Java on how to call a method from the API. You will
be give username and password for each facility that will be integrated
with NHIF. This will help NHIF Systems to identify and authorize any API
call before giving it access.


###  **SAMPLE CODE FOR CALLING AN API USING JAVA**

<!-- !!! warn ""
    From original code
 -->

```java
@Action
public void OnTest()
{
    String userName = "integrationuser";
    String password = "nhif@2018";

    try {
        String url ="https://verification.nhif.or.tz/claimsserver/api/v1/claims/SubmitFolios"; 
        String tokenEndPoint ="https://verification.nhif.or.tz/claimsserver/Token";
        
        EntityBuilder eb = new EntityBuilder();
        String jsonText = eb.getEntityString();
        CloseableHttpClient tokenClient = HttpClientBuilder.create().build();


        HttpPost request = new HttpPost(tokenEndPoint);
        request.addHeader("content-type", "application/x-www-formurlencoded");
        
        String entityBody="username= integrationuser &password=nhif@2018&grant_type=password";    
        StringEntity entity = new StringEntity(entityBody);
        request.setEntity(entity);
        
        HttpResponse result = tokenClient.execute(request);
        String stringToParse = EntityUtils.toString(result.getEntity(), "UTF-8");
        
        JSONParser parser = new JSONParser();
        JSONObject json = (JSONObject)
        parser.parse(stringToParse);

        String tokenString = json.get("access_token").toString();
        String bearer = String.format("Bearer %1s",tokenString );
        
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();
        HttpPost claimRequest = new HttpPost(url);
        claimRequest.addHeader("content-type", "application/json");
        claimRequest.addHeader("Authorization", bearer);
        
        StringEntity claimEntity = new StringEntity(jsonText);
        claimRequest.setEntity(claimEntity);
        
        HttpResponse claimResult = httpClient.execute(claimRequest);
        String claim = EntityUtils.toString(claimResult.getEntity(), "UTF-8");
        
        this.jTextField1.setText(claim);
    }
    catch(IOException e)
    {
        System.out.println(e.getMessage());
    }
    catch (Exception e){
        System.out.println(e.getMessage());
    }
}
```



### Using **EntityBuilder** 



```java
package hmacjava;

/**
 *
 * @author arashid 
 */
public class EntityBuilder {
    public String getEntityString()
    {
        StringBuffer sb =  new StringBuffer(); 

        sb.append("{");
        sb.append("\"entities\":[");
        sb.append("{");
        sb.append("\"FacilityCode\":\"01099 \",");
        sb.append("\"ClaimYear\":6,");
        sb.append("\"ClaimMonth\":7,");
        sb.append("\"FolioNo\":1,");
        sb.append("\"SerialNo\":\"SN00099\",");
        sb.append("\"CardNo\":\"308900035308\",");
        sb.append("\"FirstName\":\"Flora\",");
        sb.append("\"LastName\":\"Mataba\",");
        sb.append("\"Gender\":\"Female\",");
        sb.append("\"DateOfBirth\":\"19 74 - 01 - 23 \",");
        sb.append("\"Age\":30.0,");
        sb.append("\"TelephoneNo\":\"0686155255\",");
        sb.append("\"PatientFileNo\":null,");
        sb.append("\"PatientFile\":\"GQ8XQAYFAiEMfN0qD0COTgMX......\",");
        sb.append("\"AuthorizationNo\":null,");
        sb.append("\"AttendanceDate\":\"2014- 09 - 01 \",");
        sb.append("\"PatientTypeCode\":\"OUT\",");
        sb.append("\"DateAdmitted\":null,");
        sb.append("\"DateDischarged\":null,");
        sb.append("\"PractitionerNo\":\"12345\",");
        sb.append("\"PractitionerName\":\"Dr.William Herbert\",");
        sb.append("\"PractitionerName\":1,");
        sb.append("\"FolioDiseases\":[");
        sb.append("{");
        sb.append("\"DiseaseCode\":\"084\",");
        sb.append("\"Remarks\":null,");
        sb.append("\"CreatedBy\":\"Administrator\",");
        sb.append("\"DateCreated\":\"2015- 01 - 23 \",");
        sb.append("\"LastModifiedBy\":\"Administrator\",");
        sb.append("\"LastModified\":\"2015- 01 - 23 \"");
        sb.append("}");
        sb.append("],");
        sb.append("\"FolioItems\":[");
        sb.append("{");
        sb.append("\"ItemCode\":\"11\",");
        sb.append("\"OtherDetails\":null,");
        sb.append("\"ItemQuantity\":1,");
        sb.append("\"UnitPrice\":2000.00,");
        sb.append("\"AmountClaimed\":.00,");
        sb.append("\"ApprovalRefNo\":\"NHIF/REF/201000024\",");
        sb.append("\"CreatedBy\":\"Administrator\",");
        sb.append("\"DateCreated\":\"2015-01-23 \",");
        sb.append("\"LastModifiedBy\":\"Administrator\",");
        sb.append("\"LastModified\":\"2015-01-23 \"");
        sb.append("},");
        sb.append("{");
        sb.append("\"ItemCode\":\"11533\",");
        sb.append("\"OtherDetails\":null,");
        sb.append("\"ItemQuantity\":1,");
        sb.append("\"UnitPrice\":1950.00,");
        sb.append("\"AmountClaimed\":.00,");
        sb.append("\"ApprovalRefNo\":null,");
        sb.append("\"CreatedBy\":\"Administrator\",");
        sb.append("\"DateCreated\":\"2015-01 - 23 \",");
        sb.append("\"LastModifiedBy\":\"Administrator\",");
        sb.append("\"LastModified\":\"2015-01 - 23 \"");
        sb.append("},");
        sb.append("{");

        sb.append("\"ItemCode\":\"11004\",");
        sb.append("\"OtherDetails\":null,");
        sb.append("\"ItemQuantity\":3,");
        sb.append("\"UnitPrice\":100.00,");
        sb.append("\"AmountClaimed\":300.00,");
        sb.append("\"ApprovalRefNo\":null,");
        sb.append("\"CreatedBy\":\"Administrator\",");
        sb.append("\"DateCreated\":\"2015- 01 -23 \",");
        sb.append("\"LastModifiedBy\":\"Administrator\",");
        sb.append("\"LastModified\":\"2015- 01 -23 \"");
        sb.append("},");
        sb.append("{");
        sb.append("\"ItemCode\":\"5039\",");
        sb.append("\"OtherDetails\":null,");
        sb.append("\"ItemQuantity\":2,");
        sb.append("\"UnitPrice\":3000.00,");
        sb.append("\"AmountClaimed\":6000.00,");
        sb.append("\"ApprovalRefNo\":null,");
        sb.append("\"CreatedBy\":\"Administrator\",");
        sb.append("\"DateCreated\":\"2015- 01 -23 \",");
        sb.append("\"LastModifiedBy\":\"Administrator\",");
        sb.append("\"LastModified\":\"2015- 01 - 23 \"");
        sb.append("},");
        sb.append("{");
        sb.append("\"ItemCode\":\"7402\",");
        sb.append("\"OtherDetails\":null,");
        sb.append("\"ItemQuantity\":1,");
        sb.append("\"UnitPrice\":20000.00,");
        sb.append("\"AmountClaimed\":20000.00,");
        sb.append("\"ApprovalRefNo\":null,");
        sb.append("\"CreatedBy\":\"Administrator\",");
        sb.append("\"DateCreated\":\"2015-01-23 \",");
        sb.append("\"LastModifiedBy\":\"Administrator\",");
        sb.append("\"LastModified\":\"2015- 01 - 23 \"");
        sb.append("}");
        sb.append("],");
        sb.append("\"CreatedBy\":\"Administrator\",");
        sb.append("\"DateCreated\":\"2015-01-23 \",");
        sb.append("\"LastModifiedBy\":\"Administrator\",");
        sb.append("\"LastModified\":\"2015-01 - 26 \"");
        sb.append("},");
        sb.append("{");
        sb.append("\"FacilityCode\":\"01099\",");
        sb.append("\"ClaimYear\":2016,");
        sb.append("\"ClaimMonth\":7,");
        sb.append("\"FolioNo\":2,");
        sb.append("\"SerialNo\":\"13/109998777\",");
        sb.append("\"CardNo\":\"109900035308\",");
        sb.append("\"FirstName\":\"Amour\",");
        sb.append("\"LastName\":\"Rashid\",");
        sb.append("\"Gender\":\"Male\",");
        sb.append("\"DateOfBirth\":\"1974- 01 - 23 \",");
        sb.append("\"Age\":40.0,");


        sb.append("\"TelephoneNo\":\"0686155255\",");
        sb.append("\"PatientFileNo\":null,");
        sb.append("\"PatientFile\":\"GQ8XQAYFAiEMfN0qD0COTgMX......\",");
        sb.append("\"AuthorizationNo\":null,");
        sb.append("\"AttendanceDate\":\"2014- 09 - 01 \",");
        sb.append("\"PatientTypeCode\":\"OUT\",");
        sb.append("\"DateAdmitted\":null,");
        sb.append("\"DateDischarged\":null,");
        sb.append("\"PractitionerNo\":null,");
        sb.append("\"PractitionerName\":\"Dr.WilliamHerbert\",");
        sb.append("\"PractitionerName\":1,");
        sb.append("\"FolioDiseases\":[");
        sb.append("{");
        sb.append("\"DiseaseCode\":\"084\",");
        sb.append("\"Remarks\":null,");
        sb.append("\"CreatedBy\":\"Administrator\",");
        sb.append("\"DateCreated\":\"2015- 01 - 23 \",");
        sb.append("\"LastModifiedBy\":\"Administrator\",");
        sb.append("\"LastModified\":\"2015- 01 - 23 \"");
        sb.append("}"); 
        sb.append("],");
        sb.append("\"FolioItems\":[");
        sb.append("{");
        sb.append("\"ItemCode\":\"5036\",");
        sb.append("\"OtherDetails\":null,");
        sb.append("\"ItemQuantity\":1,");
        sb.append("\"UnitPrice\":3200.00,");
        sb.append("\"AmountClaimed\":3200.00,");
        sb.append("\"ApprovalRefNo\":null,");

        sb.append("\"CreatedBy\":\"Administrator\",")
        sb.append("\"DateCreated\":\"2015- 01 -23 \",");
        sb.append("\"LastModifiedBy\":\"Administrator\",");
        sb.append("\"LastModified\":\"2015- 01 -23 \"");

        sb.append("}");
        sb.append("],");
        sb.append("\"CreatedBy\":\"Administrator\",");
        sb.append("\"DateCreated\":\"2015- 01 -23 \",");
        sb.append("\"LastModifiedBy\":\"Administrator\","); 
        sb.append("\"LastModified\":\"2015-01 - 28 \""); 
        sb.append("},"); 
        sb.append("{");
        sb.append("\"FacilityCode\":\"01099\",");
        sb.append("\"ClaimYear\":2016,");
        sb.append("\"ClaimMonth\":7,");
        sb.append("\"FolioNo\":3,");
        sb.append("\"SerialNo\":null,");
        sb.append("\"CardNo\":\"01-NHIF241\",");
        sb.append("\"FirstName\":\"Amour\",");
        sb.append("\"LastName\":\"Hamad\",");
        sb.append("\"Gender\":\"Male\",");
        sb.append("\"DateOfBirth\":\"1974- 01 -23 \","); 
        sb.append("\"Age\":40.0,");
        sb.append("\"TelephoneNo\":\"0686155255\",");
        sb.append("\"PatientFileNo\":null,");
        sb.append("\"PatientFile\":\"GQ8XQAYFAiEMfN0qD0COTgMX......\",");
        sb.append("\"AuthorizationNo\":null,");
        sb.append("\"AttendanceDate\":\"2014- 09 - 01 \",");
        sb.append("\"PatientTypeCode\":\"OUT\",");
        sb.append("\"DateAdmitted\":null,");
        sb.append("\"DateDischarged\":null,");
        sb.append("\"PractitionerNo\":null,");
        sb.append("\"PractitionerName\":\"Dr.WilliamHerbert\",");
        sb.append("\"PractitionerName\":1,");
        sb.append("\"FolioDiseases\":[");
        sb.append("{");
        sb.append("\"DiseaseCode\":\"084\",");

        sb.append("\"Remarks\":null,");
        sb.append("\"CreatedBy\":\"Administrator\",");
        sb.append("\"DateCreated\":\"2015- 01 - 23 \",");
        sb.append("\"LastModifiedBy\":\"Administrator\",");
        sb.append("\"LastModified\":\"2015- 01 - 23 \"");
        sb.append("}"); sb.append("],");
        sb.append("\"FolioItems\":[");
        sb.append("{");
        sb.append("\"ItemCode\":\"5059\",");
        sb.append("\"OtherDetails\":null,");
        sb.append("\"ItemQuantity\":1,");
        sb.append("\"UnitPrice\":3800.00,");
        sb.append("\"AmountClaimed\":3800.00,");
        sb.append("\"ApprovalRefNo\":null,");
        sb.append("\"CreatedBy\":\"Administrator\",");
        sb.append("\"DateCreated\":\"2015- 01 -23 \",");
        sb.append("\"LastModifiedBy\":\"Administrator\",");
        sb.append("\"LastModified\":\"2015- 01 -23 \"");
        sb.append("}");
        sb.append("],");
        sb.append("\"CreatedBy\":\"Administrator\",");
        sb.append("\"DateCreated\":\"2015- 01 -23 \",");
        sb.append("\"LastModifiedBy\":\"Administrator\",");
        sb.append("\"LastModified\":\"2015- 01 -26 \""); 
        sb.append("}"); sb.append("]");
        sb.append("}");

        return sb.toString();
    }
}
```



For a particular programming Language such as PHP, JavaScript, C# etc, Please send use a request to [it@nhif.or.tz](mailto:it@nhif.or.tz) and we will send you the sample code.



### SUBMITTING DOCUMENTS:



The field in the Folio Object in the JSON Schema with a name `"PatientFile"` is supposed to contain the PDF Document that contain all details about the patient treatment for that particular folio. The document need to be submitted as Base 64 String. At the end, all the contents of the JSON document should be submitted as a single Complex object as given in sample document below.


```json
{
  "entities": [
    {
      "FolioID": "817d6e75-3bef-4d25-a2e1-a3bc009530ab",
      "ClaimYear": 2016,
      "ClaimMonth": 7,
      "FolioNo": 1,
      "SerialNo": "SN00 099",
      "CardNo": "308900035308",
      "FirstName": "Flora",
      "LastName": "Mataba",
      "Gender": "Female",
      "DateOfBirth": "1974- 01 -23T16:56:20.287",
      "Age": 30.0,
      "TelephoneNo": "0686155255",
      "PatientFileNo": null,
      "PatientFile": "GQ8XQAYFAiEMfN0qD0COTgMX......",
      "AuthorizationNo": null,
      "AttendanceDate": "2014- 09 -01T00:00:00",
      "PatientTypeCode": "OUT",
      "DateAdmitted": null,
      "DateDischarged": null,
      "PractitionerNo": "12345",
      "CreatedBy": "Administrator",
      "DateCreated": "2015- 01 -23T16:56:20.223",
      "LastModifiedBy": "Administrator",
      "LastModified": "2015-01 - 2 6T12:31:25.097",
      "FolioDiseases": [
        {
          "FolioDiseaseID": "e9429e1c-f892-40ae-8c0a-a3bc0095681f",
          "DiseaseCode": "084",
          "FolioID": "817d6e75-3bef-4d25-a2e1-a3bc009530ab",
          "Remarks": null,
          "CreatedBy": "Administrator",
          "DateCreated": "2015 - 01 -23T16:56:20.287",
          "LastModifiedBy": "Administrator",
          "LastModified": "2015-01 - 23T16:56:20.287"
        }
      ],
      "FolioItems": [
        {
          "FolioItemID": "e0d30408- 1863 - 4eb4-8cce-a3bc00957501",
          "FolioID": "817d6e75-3bef-4d25-a2e1-a3bc009530ab",
          "ItemCode": "11",
          "OtherDetails": null,
          "ItemQuantity": 1,
          "UnitPrice": 2000.0,
          "AmountClaimed": 2000.0,
          "ApprovalRefNo": "NHIF/REF/201000024",
          "CreatedBy": "Administrator",
          "DateCreated": "2015- 01 -23T16:56:20.35",
          "LastModifiedBy": "Administrator",
          "LastModified": "2015-01 - 23T16:56:20.35"
        },
        {
          "FolioItemID": "84ae41a0-514c-489d-8e6f-a3bc00958289",
          "FolioID": "817d6e75-3bef-4d25-a2e1-a3bc009530ab",
          "ItemCode": "11533",
          "OtherDetails": null,
          "ItemQuantity": 1,
          "UnitPrice": 1950.0,
          "AmountClaimed": 1950.0,
          "ApprovalRefNo": "null",
          "CreatedBy": "Administrator",
          "DateCreated": "2015- 01 -23T16:56:20.37",
          "LastModifiedBy": "Administrator",
          "LastModified": "2015-01 - 23T16:56:20.37"
        },
        {
          "FolioItemID": "3c5b814c-7fbf-454b-9c9e-a3bc00985ba5",
          "FolioID": "817d6e75-3bef-4d25-a2e1-a3bc009530ab",
          "ItemCode": "11004",
          "OtherDetails": null,
          "ItemQuantity": 3,
          "UnitPrice": 100.0,
          "AmountClaimed": 300.0,
          "ApprovalRefNo": "null",
          "CreatedBy": "Administrator",
          "DateCreated": "2015- 01 -23T16:56:20.39",
          "LastModifiedBy": "Administrator",
          "LastModified": "2015-01 - 23T16:56:20.39"
        },
        {
          "FolioItemID": "338ce17c- 1655 - 417f-80e3-a3ca01019326",
          "FolioID": "817d6e75-3bef-4d25-a2e1-a3bc009530ab",
          "ItemCode": "5039",
          "OtherDetails": null,
          "ItemQuantity": 2,
          "UnitPrice": 3000.0,
          "AmountClaimed": 6000.0,
          "ApprovalRefNo": "null",
          "CreatedBy": "Administrator",
          "DateCreated": "2015- 01 -23T16:56:20.41",
          "LastModifiedBy": "Administrator",
          "LastModified": "2 015 -01 - 23T16:56:20.41"
        },
        {
          "FolioItemID": "ae3e912a-137b-48e7- 9853 -a3ca01058118",
          "FolioID": "817d6e75-3bef-4d25-a2e1-a3bc009530ab",
          "ItemCode": "7402",
          "OtherDetails": null,
          "ItemQuantity": 1,
          "UnitPrice": 20000.0,
          "AmountClaimed": 20000.0,
          "ApprovalRefNo": "null",
          "CreatedBy": "Administrator",
          "DateCreated": "2015- 01 -23T16:56:20.47",
          "LastModifiedBy": "Administrator",
          "LastModified": "2015-01 - 23T16:56:20.47"
        }
      ]
    },
    {
      "FolioID": "d3d7656b-7c3b- 4392 - b75f-a3ca01048e2b",
      "ClaimYear": 2016,
      "ClaimMonth": 7,
      "FolioNo": 2,
      "SerialNo": "13/109998777",
      "CardNo": "109900035308",
      "FirstName": "Amour",
      "LastName": "Rashid",
      "Gender": "Male",
      "DateOfBirth": "1974- 01 -23T16:56:20.287",
      "Age": 40.0,
      "TelephoneNo": "0686155255",
      "PatientFileNo": null,
      "AuthorizationNo": null,
      "AttendanceDate": "2014- 09 -01T00:00:00",
      "PatientTypeCode": "OUT",
      "DateAdmitted": null,
      "DateDischarged": null,
      "PractitionerNo": null,
      "CreatedBy": "Administrator",
      "DateCreated": "2015- 01 -23T16:56:20.247",
      "LastModifiedBy": "Administrator",
      "LastModified": "2015-01 - 28T13:31:09.297",
      "FolioDiseases": [
        {
          "FolioDiseaseID": "c4f56a4c- 6217 - 475d-b3de-a3ca0104c9dc",
          "DiseaseCode": "084",
          "FolioID": "d3d7656b-7c3b- 4392 - b75f-a3ca01048e2b",
          "Remarks": null,
          "CreatedBy": "Administrator",
          "DateCreated": "2015 - 01 -23T16:56:20.307",
          "LastModifiedBy": "Administrator",
          "LastModified": "2015-01 - 23T16:56:20.307"
        }
      ],
      "FolioItems": [
        {
          "FolioItemID": "248fa65b-dab8-4ca2- 9905 -a3ca0104cfc7",
          "FolioID": "d3d7656b-7c3b- 4392 - b75f-a3ca01048e2b",
          "ItemCode": "5036",
          "OtherDetails": null,
          "ItemQuantity": 1,
          "UnitPrice": 3200.0,
          "AmountClaimed": 3200.0,
          "ApprovalRefNo": "null",
          "CreatedBy": "Administrator",
          "DateCreated": "2015- 01 -23T16:56:20.43",
          "LastModifiedBy": "Administrator",
          "LastModified": "2015-01 - 23T16:56:20.43"
        }
      ]
    }
  ]
}
```


The screenshot below shows the layout of the sample JSON document that
can be posted on an NHIF API.



### CLAIMS RECONCILIATION



You can call the API below to retrieve list of claims that have been successful received by NHIF for reconciliation purpose


```
https://verification.nhif.or.tz/claimsServer/api/v1/claims/
getSubmittedClaims?FacilityCode=06852&ClaimYear=2019&ClaimMonth=9
```
