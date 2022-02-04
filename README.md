     .style1 { color: #0066CC; font-weight: bold; } .style2 {color: #0066CC} 

INSPECTION REST API - NODEJS
----------------------------

#### CREATED: 2/3/2022

**API ENDPOINTS & DEFINITION**

URLS:  

**DEV:**https://develop-services.appraisalworld.com/fieldinspection

**PRD:** fieldinspection  

CUSTOMER-EMAIL = CRM's appraiser email address  
CUSTOMER-PASSWORD = CRM's appraiser password  
INSPECTION-ID = API assigns each inspection a unique ID (int) during the insert-data process

DEVICE-ID/CALLER-ID:  
refer to new table in CRM-DEV-DB: property\_db.IalDevice

STATUS:  
refer to new table in CRM-DEV-DB:property\_db.IalInspStatus

**REQUIREMENT**

Every user in the system must belong to either 1 of these categories in order to store their inspections and photos on the AW server:

\-- have valid bradford membership plan ( >= Gold)  
\-- recurring billing active  
\-- is a test entry (Bt employees only with @bradfordsoftware.com or @appraisalworld.com email address)  

**TOKEN AUTHENTICATION  
Path: /Auth**

**Description:** TOKEN Authentication

**Header:** "Content-Type":"application/json"  
Authorization:Basic xxxBASE64EncodeStringHerexxx  
**Method:** POST (NOT SUPPORT GET for security)  
**Body:**empty  

**Success Response Sample:** JSON:  
{"status":true,"code":0,"message":"Success","data":{"token":"TokenJWTHere","custUID":"130","membership\_id":"50","is\_membership\_active":1}}

\- membership\_id: membership level based on what subscription user purchased  
\- is\_membership\_active: 0/1 (based on maintenance date)

**Error Response:** JSON: {"status":false,"code":(int),"message":"(string)")}  
**Error Code message**:  
110 Invalid parameters  
120 Data validation error  
130/140 Access is not authorized. User not exists.  
150 Membership level not allowed access.

**All subsequent requests must use JWTToken in the Headers**

**Header:** "Content-Type":"application/json"  
Authorization:Bearer xxxxJWTTokenFromAuthAbovexxx

**IMPORT/UPDATE INSPECTION DATA TO CRM  
Path: /ImportData**

**Description**: Import/Update inspection data to CRM

  
**Method:** POST  
**Body:** Data posted format: JSON

username (CUSTOMER-EMAIL) (string)  
password (CUSTOMER-PASSWORD) (string)  
insp\_id (int) (If id=0, new record is created. If id > 0, update this record).  
address (string)  
city (string)  
state (string)  
zipcode (string)  
apt (string)  
insp\_type (string) length=50  
insp\_date (string)  
insp\_time (string)  
status (string) length=1 (STATUS)  
revision (int) - count how many time user update this inspection  
duration (int)  
version (string) ("5" as of 2022)  
caller (int)  
assigned\_id (int) ( 0=no team, >0 assign this inspection to a team member - Team Add-on feature  
posted\_data (json blob) ==> [click to view data structure of this blob - DataStructure Version 5.  
Please contact Ms. Pam for any changes in data structure.](PostedDataStructure5.txt)

**Success Response:** JSON: {"status":true,"code":0,"message":"success","id":"OrderID (int)"}  
**Error Response:** JSON: {"status":false,"code":(int),"message":"(string)","id":(int)}  
**Error Code message**:  
110 Token Invalid  
120 Data validation error.  
130 Access is not authorized. User not exists.  
140 Data not inserted.  
150 No posted data.  
160 No inspection found, not updated.  
161 Status not updated.  
170 Service is not authorized. User exists but membership level is not valid.

**RETRIEVE INSPECTIONS SUMMARY  
Path: /GetSummary**

**Description:** Retrieve all inspection data in summary only (posted\_data in details are excluded to reduce loading time)

**Method:** POST  
**Body:**  
{  
device\_id: integer  
status:character (ex. N, or I... in table IalInspStatus)  
}

**Success Response:** JSON: {"status":true,"code":0,"message":"success","data":array}  
**Error Response:** JSON: {"status":false,"code":(int),"message":"(string)")}  
**Error Code message**:  
110 Token Invalid  
120 Data validation error  
130/140 Access is not authorized. User not exists.  
160 No inspection found.  
170 Service is not authorized. User exists but membership level is not valid.

**RETRIEVE DETAIL OF AN INSPECTION  
Path: /GetOrder**

**Description:** Retrieve all data of a specific inspection given inspection id.

**Method**: POST  
**Body**:  
{  
"insp\_id"::integer  
,"caller":integer  
}

**Success Response:** JSON: {"status":true,"code":0,"message":"success","data":JSON array}

**Response Sample:**  
{"status":true,"code":0,"message":"success","data":{"insp\_id":"3","status":"D","custUID":"130","address":"10271 S CEDARIDGE CT","city":"San Jose","state":"CA","zipcode":"95123","apt":"","insp\_type":"FullInspection","insp\_date":"2018-09-12 00:00:00","insp\_time":null,"status\_date":"2016-09-26 11:53:37","date":"2018-09-26 03:02:42","posted\_data":{"a":"test","json string here":"nothing"},"revision":"10","duration":"200","version":"1","device":"0","team\_id":"0","assigned\_id":"211","start\_date":null,"end\_date":null}}

**Error Response:** JSON: {"status":false,"code":(int),"message":"(string)")}  
**Error Code message**:  
110 Token Invalid  
120 Data validation error  
130/140 Access is not authorized. User not exists.  
160 No inspection found.  
150/170 Service is not authorized. User exists but membership level is not valid.

**DELETE AN INSPECTION  
Path: /DeleteOrder**

**Description:** Update status of an inspection to "R" given inspection id (Not physically deleted)

**Method:** POST  
**Body:**  
{  
"insp\_id":integer  
}

**Success Response:** JSON: {"status":true,"code":0,"message":"success"}  
**Error Response:** JSON: {"status":false,"code":(int),"message":"(string)")}  
**Error Code message**:  
110 Token Invalid  
120 Data validation error  
130/140 Access is not authorized. User not exists.  
160 Inspection not found.  
150/170 Service is not authorized. User exists but membership level is not valid.  

**INSPECTION ACKNOWLEDGEMENT  
Path: /Ack (This endpoint supports IAL on iPad deviceID=3 & 6 only)**

**Description:** Send ack by IAL or CF, Ack will update inspection status automatically based on device id and current status of a given inspection ID.  
**Notes:** if current order has "I" status and assigned\_id not match custUID, which means somebody else is working on it, DONOT update record and must return \[data\]

If DEVICE-ID = 1 and Status = 'C', then update this inspection status to "D".  
If DEVICE-ID = 3 or 6 and Status = 'N', then update this inspection status to "I".

**Method**: POST  
**Body**:  
{  
"insp\_id":integer  
,"caller"::integer  
}

**Success Response:** JSON: {"status":true,"code":0,"message":"success","data":{"Status":"I","AssignedToId":"211","AssignedToName":"binhtest123 MyLast","Revision":"10"}}  
**Error Response:** JSON: {"status":false,"code":(int),"message":"(string)")}  
**Error Code message**:  
110 Token Invalid  
120 Data validation error  
130/140 Access is not authorized. User not exists.  
160 No inspection found. Status is not updated.  
150/170 Service is not authorized. User exists but membership level is not valid.

**INSPECTION ACKNOWLEDGEMENT  
Path: /GetOrderAck**

**Description**: Sent ack by IAL or CF/Client, Ack will update inspection status automatically based on device id and current status of a specific inspection.

If DEVICE-ID = 1 and Status = 'C', then update this inspection status to "D".  
If DEVICE-ID = 3 or 6 and Status = 'N', then update this inspection status to "I".

**Method**: POST  
**Body**:  
{  
"insp\_id":integer  
,"caller"::integer  
}

**Success Response**: JSON: {"status":true,"code":0,"message":"success","data":""}  
**Error Response**: JSON: {"status":false,"code":(int),"message":"(string)")}  
**Error Code message**:  
110 Token Invalid  
120 Data validation error  
130/140 Access is not authorized. User not exists.  
160 No inspection found. Status is not updated.  
150/170 Service is not authorized. User exists but membership level is not valid.  
180 Status has already been set as N
