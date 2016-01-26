##Version 2015-11-09
#INB Digital Order Form

#Server to Server REST API Documentation
This document intends to document the REST API for the DVmobile node.js server and the Imagine Nation Books backend ordering system. Both servers will expose a REST interface made available to the other.

- [Authentication](#auth)
- [INB Exposed API](#inb)
- [DVmobile Exposed API](#dvmobile)

***
[Authentication](id:auth)
===
The Server to Server API uses the Basic Authentication method described in [rfc2617](http://tools.ietf.org/html/rfc2617) / [wiki](https://en.wikipedia.org/wiki/Basic_access_authentication).

Add the HTTP ```Authentication``` header to every request. The value of the header should be the word ```Basic``` with a Base64 encoded string of the username joined with the password by a colon. ```username:password```.

###### Example Authorization Header
Using the username ```some_username```, and the password ```some_password```, our resulting Authorization Header would look like the following:

```
Basic c29tZV91c2VybmFtZTpzb21lX3Bhc3N3b3Jk
```

###Error Responses
Status | error | message | Description
-----| ------ | ------ | ----- |
401|invalid_scheme | N/A | Must use the 'Basic' Authentication scheme in the Authorization Header
401|invalid_api_key | N/A | Invalid username credential for the Basic Authentication. Check with DVmobile for a valid api key to use.
401|invalid_password | N/A | Invalid password credential for the Basic Authentication. Check with DVmobile for a valid password to use.

######Example Errors

```
{ "error": "invalid_scheme" }
{ "error": "invalid_api_key" }
{ "error": "invalid_password" }
```


***
[INB Exposed API](id:inb)
===
The following describes the REST API that will be made available by Imagine Nation Books. DVmobile servers will use this API to send information to Imagine Nation Books.

- [Place Order](#place_order)

## [ORDER: Place an Order](id:place_order)
This endpoint will update the status of an order.

### URI
```
POST /api/v1/Order
```

### Parameters

Name | Description |
-----| ------ |
order_key|The DVmobile unique identifier for this order|
lead_id|The INB unique identifier for the lead the order was placed at|
rep_code|The INB unique Rep identifier|
event_id|A string reference to an event|
payment_method|The payment method used (cash, check, card, paypal)|
total|The total order amount|
status|A string token representing the status of the order|
order_dt|Datetime Timestamp of when the order was received by the server|
first_name | The Customer's first name
last_name | The Customer's last name
email | The Customer's email address
phone | The Customer's phone number
items|A list of items|


######Example Payload
```
{
	"order_key": "DFA3476",
	"lead_id":1,
	"rep_code": "GOLDJ1",
	"event_id": "123456",
	"payment_method":"card",
	"total":20.00,
	"status":"PLACED",
	"order_dt":"2015-05-04T22:08:01.000Z",
	"first_name":"John",
	"last_name":"Smith",
	"email": "john@smith.com",
	"phone": "3038675309",
	"items": [
		{"id":1,"qty":1,"table_price":10.00},
		{"id":2,"qty":1,"table_price":10.00}
	]
}

```

### Success Response

Key | Description |
-----| ------ |
success|Boolen that indicates the server's idea of success|

######Example Response

```
{
	success: true
}
```

***
[DVmobile API](id:dvmobile)
===================

The following describes the REST API that will be made available by DVmobile. INB servers will use this API to send information to DVmobile servers.

- [Update Order Status](#update_order)
- [Create or Update an Item](#create_item)
- [Create or Update a Rep](#create_rep)
- [Create or Update a Lead](#create_lead)
- [Update Displays](#displays)
- [Add or Update a Single Inventoried Item](#inventory_single)
- [Add or Update Multiple Inventoried Items](#inventory_multiple)

## [ORDER: Update Order Status](id:update_order)
This endpoint will update the status of an order.

### URI
```
POST /api/v1/Order/:order_key
PUT  /api/v1/Order/:order_key
```

### Parameters

Name | Description |
-----| ------ |
status|The status of the Order|

######Example Payload
```
{
	"status":"FULFILLED"
}

```

### Success Response

Key | Description |
-----| ------ |
success|Boolen that indicates the server's idea of success|
order|The full order record|

######Example Response

```
{
	success: true,
	order: {
		id: 135,
        di: 0,
        cr: '2015-09-10T17:40:53.000Z',
        mo: '2015-09-10T17:40:53.000Z',
        lead_id: 2,
        user_id: null,
        total: '1.99',
        method: 'card',
        status: 'FULFILLED',
        payment_id: 'PAY-4TF562331H976980VKXGLIQI',
        nm: 'Test Order',
        eml: 'test@dv-mobile.com',
        phone: '5555555555',
        rep_code: 'DVMOBL',
        source: 'Kiosk - iOS',
        order_key: 135,
        order_ref: 'DFA135'
	}
}
```

###Error Responses
Status | error | message | Description
-----| ------ | ------ | ----- |
400|MissingParam| status  | Missing 'status' param in request
400|InvalidParam| status  | Invalid value for status.
404|ORDER| Unknown Order for order_key | Unable to locate the Order.


######Example Errors

```
{ "error": "MissingParam", "message": "status" }
{ "error": "ORDER", "message": "Unknown order for order_key" }
```  


## [ITEM: Create or Update an Item](id:create_item)
This endpoint will either create or update an item for a given item_id. If the item_id does not exist, one will be created using the information given in the request payload. If the item_id already exists, all information in the request payload will overwrite the item's existing data.

### URI
```
POST /api/v1/Item/:item_id
PUT  /api/v1/Item/:item_id
```

### Parameters

Name | Description |
-----| ------ |
retail_cost|The suggested price of the item|
nm|The simple name of the item|
img_url|Image URL for mobile clients to use|

######Example Payload
```
{
	"retail_cost":	"15.99",
	"nm":			"Harry Potter and the Goblet of Fire",
	"img_url":		"http://cf.booksarefun.com/webimages/thumb/36671.jpg"
}

```

### Success Response

Key | Description |
-----| ------ |
success|Boolen that indicates the server's idea of success|
item|The full item record|

######Example Response

```
{
	success: true,
 	item: {
 		id: 7,
		di: 0,
		cr: '2015-05-04T22:08:01.000Z',
		mo: '2015-05-04T22:08:01.000Z',
		retail_cost: 15.99,
		name: "Harry Potter and Goblet of Fire",
		img_url: 'http://cf.booksarefun.com/webimages/thumb/36671.jpg'
	}
}
```

###Error Responses
Status | error | message | Description
-----| ------ | ------ | ----- |
400|MissingParam| nm  | Missing 'nm' param in request
400|MissingParam| img_url  | Missing 'img_url' param in request
400|MissingParam| retail_cost  | Missing 'retail_cost' param in request
400|InvalidParam| item_id  | item_id is not an integer


######Example Errors

```
{ "error": "MissingParam", "message": "nm" }
```

## [REP: Create or Update a Rep](id:create_rep)
This endpoint will either create or update a rep for a given rep_code. If the Rep does not yet exist, it will be created, otherwise the rep will be updated with the information contained in the payload.

### URI
```
POST /api/v1/Rep/:rep_code
PUT  /api/v1/Rep/:rep_code
```

### Parameters

Name | Description |
-----| ------ |
first_nm|The Rep's First Name|
last_nm|The Rep's Last Name|
email|The Rep's email address|
phone|A phone number that will be displayed in the mobile app|

######Example Payload
```
{
	"first_nm":		"John",
	"last_nm":		"Doe",
	"email":		"johnd@e.com",
	"phone":		"3038675309",
}

```

### Success Response

Key | Description |
-----| ------ |
success|Boolen that indicates the server's idea of success|
rep|The full Rep record|

######Example Response

```
{
	success: true,
 	rep: {
 		id: 2863,
		di: 0,
		cr: '2015-05-04T22:08:01.000Z',
		mo: '2015-05-04T22:08:01.000Z',
		rep_code: 'JOHNDOE',
		first_nm: 'John',
		last_nm: 'Doe',
		email: 'johnd@e.com',
		phone: '3038675309'
	}
}
```

###Error Responses
Status | error | message | Description
-----| ------ | ------ | ----- |
400|MissingParam| first_nm  | Missing 'first_nm' param in request
400|MissingParam| last_nm  | Missing 'last_nm' param in request
400|MissingParam| email  | Missing 'email' param in request
400|MissingParam| phone  | Missing 'phone' param in request


## [LEAD: Create or Update a Lead](id:create_lead)
This endpoint will either create or update a single lead for a given lead_id. If the lead does not yet exist, it will be created, otherwise the lead will be updated with the information contained in the payload.

### URI
```
POST /api/v1/Lead/:lead_id
PUT  /api/v1/Lead/:lead_id
```

### Parameters

Name | Description |
-----| ------ |
rep_code|The Rep's associated with this lead|
nm|The User visible name of this lead|
physAddr1|The leads' Address|
physAddr2|The leads' Address|
physAddr3|The leads' Address|
physCity|The lead's City|
physState|The lead's State|
physPostal|The lead's Postal Zip Code|
physCounty|The lead's county|
physLat|The lead's Latitude|
physLong|The lead's Longitude|

######Example Payload
```
{
	"rep_code":		"GOLDJ1",
	"nm":			"James Woods Elementary",
	"physAddr1":	"123 Main St",
	"physCity":		"Denver",
	"physState":	"CO",
	"physPostal":	"80205",
	"physCounty":	"Denver",
	"physLat":		"39.754918",
	"physLong":		"-105.0051347",
}
```

### Success Response

Key | Description |
-----| ------ |
success|Boolen that indicates the server's idea of success|
lead|The full Lead record|

######Example Response

```
{
	success: true,
 	lead: {
 		id: 7,
		di: 0,
		cr: '2015-05-04T22:08:01.000Z',
		mo: '2015-05-04T22:08:01.000Z',
		rep_code: 'GOLDJ1',
		nm:	'James Woods Elementary',
		physAddr1: '123 Main St',
		physAddr2: '',
		physAddr3: '',
		physCity: 'Denver',
		physState: 'CO',
		physPostal: '80205',
		physCounty: 'Denver',
		physLat: '39.754918',
		physLong: '-105.0051347',
	}
}
```

###Error Responses
Status | error | message | Description
-----| ------ | ------ | ----- |
400|MissingParam| nm  | Missing 'name' param in request
404|REP| Unknown Rep for Rep Code | No Rep is associated with the rep_code supplied. Add the Rep to the database and then try again.




## [LEAD: Update Display(s) for Lead(s)](id:displays)
This endpoint will update display information for either a single lead or multiple leads. It accepts a list of lead_ids as well as a list of 'displays'. A display contains a cycle_ref, eventStartDt, eventEndDt and a list of items. Multiple displays with different dates can be assigned to a lead or list of leads.

### URI
```
POST /api/v1/Display
```

### Parameters

Name | Description |
-----| ------ |
leads|A list of lead_ids|
displays|A list of display objects|


######Example Payload
```
{
	"leads":	[7,10,400],
	"displays":	[
					{
						"event_id": 	"2015-1",
						"eventStartDt":	"2015-09-21T17:27:58.000Z",
						"eventEndDt":	"2015-09-30T17:27:58.000Z",
						"item_ids": [566,36786]
					}
				]

}
```

### Success Response

Key | Description |
-----| ------ |
success|Boolen that indicates the server's idea of success|

######Example Response

```
{
	success: true
}
```

###Error Responses
Status | error | message | Description
-----| ------ | ------ | ----- |
400|MissingParam| leads  | Missing 'leads' param in request
400|MissingParam| displays  | Missing 'displays' param in request


## [INVENTORY: Add or Update a single inventoried item](id:inventory_single)
This endpoint will add or update inventory information for a particular Rep's Item.

### URI
```
POST /api/v1/Rep/:rep_code/Item/:item_id
PUT  /api/v1/Rep/:rep_code/Item/:item_id
```

### Parameters

Name | Description |
-----| ------ |
qty|The *sellable* quantity of an item|
table_price|The actual sale price of the product|

######Example Payload
```
{
	"qty":				"100",
	"table_price":		"7.99"
}
```

### Success Response

Key | Description |
-----| ------ |
success|Boolen that indicates the server's idea of success|
inventory|The full inventory record|

######Example Response

```
{
	success: true,
 	inventory: {
		di: 0,
		cr: '2015-05-04T22:08:01.000Z',
		mo: '2015-05-04T22:08:01.000Z',
		item_id: 7,
		rep_code: 'GOLDJ1',
		qty: 100,
		table_price: 7.99
	}
}
```

###Error Responses
Status | error | message | Description
-----| ------ | ------ | ----- |
400|MissingParam| table_price  | Missing 'table_price' param in request
400|MissingParam| qty  | Missing 'qty' param in request


## [INVENTORY: Add or Update multiple inventoried items](id:inventory_multiple)
This endpoint will add or update inventory information for multiple items for a given Rep.

### URI
```
POST /api/v1/Rep/:rep_code/Item/multiple
PUT  /api/v1/Rep/:rep_code/Item/multiple
```

### Parameters

Name | Description |
-----| ------ |
inventory|A list of item inventory objects.|

######Example Payload
```
{
	inventory: [
		{
			"item_id":			"15",
			"qty":				"100",
			"table_price":		"7.99"
		},
		{
			"item_id":			"20",
			"qty":				"50",
			"table_price":		"4.99"
		}
	]
}
```

### Success Response

Key | Description |
-----| ------ |
success|Boolen that indicates the server's idea of success|
inventory|An Array of inventory records|

######Example Response

```
{
	success: true,
 	inventory: [
 		{
			di: 0,
			cr: '2015-05-04T22:08:01.000Z',
			mo: '2015-05-04T22:08:01.000Z',
			rep_code: 'GOLDJ1,
			item_id: 15,
			qty: 100,
			table_price: 7.99
		},
		{
			di: 0,
			cr: '2015-05-04T22:08:01.000Z',
			mo: '2015-05-04T22:08:01.000Z',
			rep_code: 'GOLDJ1,
			item_id: 20,
			qty: 50,
			table_price: 4.99
		}
	]
}
```

###Error Responses
Status | error | message | Description
-----| ------ | ------ | ----- |
400|MissingParam| inventory  | Missing 'inventory' param in request
