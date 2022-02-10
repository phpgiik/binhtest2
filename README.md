#h1
# h1
para1

para2

br1
**br2**

## /Login

***Headers:***
Authorization:Basic Base64Of Email:Password

***Body:*** empty

***Response:***

{
"status": true,

"data":

	{
	"token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJ",
	"CustUID": 130,
	"TeamID": 133,
	"membership_id": "51",
	"is_membership_active": 1
	}
	
}

**All request beside /Login needs headers Authorization:**

**Headers:**

**Authorization:Bearer xxxJWTTOKENxxxeyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6ImNybSJ9**

## /GetSummary

***Headers:***
Authorization:Basic Base64Of Email:Password

***Body:*** 

{

  "device_id":3,  
  "CustUID":130,    
  "status":"NPI",  
  "from_date":"01/09/2022"
  
}

***Response:***

{

"status": true,

"data":

[
	{
	"insp_id": 1000001,
	"status": "N",
	"CustUID": 130,
	"address": "2132 Huxley Way",
	"city": "Modesto",
	"state": "CA",
	"zipcode": "95356",
	"apt": null,
	"insp_type": "FULL Inspection",
	"insp_date": "02/10/2022",
	"insp_time": "11:30 AM",
	"status_date": "2022-02-10T08:00:00.000Z",
	"date": "2022-02-08T17:19:15.000Z",
	"posted_data": "JSON STRING",
	"revision": 2,
	"duration": 0,
	"version": "6",
	"device": 3,
	"team_id": 0,
	"assigned_id": 0,
	"PropertyUID": 331

	},
	{"insp_id":
	...
	}
	]

}

