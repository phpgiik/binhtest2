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
	{"insp_id": 1000001, "status": "N", "CustUID": 130, "address": null, "city": null,…},
	{"insp_id": 1000002, "status": "P", "CustUID": 130, "address": null, "city": null,…},
	]

}
