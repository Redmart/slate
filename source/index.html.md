---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - scala

toc_footers:
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

This is the official documentation of RedMart's Marketplace **Partner API**. Using this private API, you'll be able to manage your inventory, products, orders, pickups and more! 

The API effort is still very much Work In Progress. Treat this documentation as a beta version likely to change in the near future.

We provide language bindings in Shell and Scala. You can view code samples in the dark pane on the right. Switch the programming language with the tabs at the top.

# Registration

The Partner API uses the [OAuth2 authorization framework](https://tools.ietf.org/html/rfc6749) and supports the [Client Credentials](https://tools.ietf.org/html/rfc6749#section-4.4) flow.

In a nutshell : 

 1. Register your Client Application in [partner-portal](https://partners.redmart.com)
 2. Save (securely) your CLIENT_ID and CLIENT_SECRET into your Client Application's database
 3. Client Application requests an Access Token from Partner API using its CLIENT_ID and CLIENT SECRET
 4. Client Application uses the Access Token to call Partner API endpoints
 5. Once Client Application starts receiving [*401 Unauthorized*](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.4.2) responses to its calls, it means the Access Token has expired. It then needs to request another Access Token (step 3) 
 
<aside class="warning">The registration phase in partner-portal (step 1) won't actually be part of the initial release and will be invite-only. Contact RedMart directly, provide your Client Application name and we'll register it for you. We'll then forward your CLIENT_ID and CLIENT_SECRET.</aside>

# Request a Token

<a id="request-a-token"></a>

> To request a token, use this code:

```shell
# Test hostname : partner-api.alpha.redmart.com
# Prod hostname : partner-api.redmart.com
curl --include --insecure --request POST \
     --header "content-type: application/x-www-form-urlencoded" \
     --data "client_id={MY_CLIENT_ID}&grant_type=client_credentials&client_secret={MY_CLIENT_SECRET}" \
     "https://{HOSTNAME}/oauth2/token"
```

```scala
// TODO
```

> Make sure to replace `{MY_CLIENT_ID}` and `{MY_CLIENT_SECRET}` with the actual values provided during Registration.

The Client Application makes a request to the `/oauth2/token` endpoint by sending the following parameters in the "application/x-www-form-urlencoded" format

Parameter | Required | Description
--------- | ------- | -----------
client_id | true | The Client ID provided during Registration
client_secret | true | The Client Secret provided during Registration 
grant_type | true | The value MUST be set to "client_credentials"
scope | false | Optional string describing the scope of the access request

> [200 OK](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1) response:

```json
{  
   "token_type":"bearer",
   "access_token":"{access-token}",
   "expires_in":7200 // this is in seconds
}
```

# Pickup Locations

A Product can be picked from within one Pickup Location.
A Marketplace Seller can have several Pickup Locations.

<aside class="warning">
To perform all <b>Pickup Locations</b> operations, you must always send your oauth2 access-token (see the <a href="#request-a-token">Request a Token</a> section).
Look at the 'Authorization' header in the code samples.
</aside>

## Get all Pickup Locations

<a id="get-all-pickup-locations"></a>

> Code samples

```shell
# Test hostname : partner-api.alpha.redmart.com
# Prod hostname : partner-api.redmart.com
curl --include --insecure \
     -X GET "https://{HOSTNAME}/v1/pickup-locations?page=1&pageSize=50" \
     -H 'Accept: application/json' \
     -H 'Authorization: Bearer {access-token}'

```

```scala
// TODO
```

`GET /v1/pickup-locations`

*Querying and filtering pickup locations*

<h3 id="getpickup-locations-parameters">Query Parameters</h3>

|Parameter|In|Required|Default|Description|
|---|---|---|---|---|
|page|query|false|1|The page number, must be >= 1
|pageSize|query|false|50|Number of items on one page, must be >= 1 and <= 100

> 200 Response

```json
{
  "page": 1,
  "pageSize": 50,
  "items": [
    {
      "addressLine1": "1 Main Street",
      "city": "Singapore",
      "name": "ABC Merchant",
      "country": "Singapore",
      "postalCode": "123456",
      "id": "23789",
      "addressLine2": "#02-15"
    }
  ],
  "total": 1
}
```

## Get one Pickup Location

<a id="get-one-pickup-location"></a>

> Code samples

```shell
# Test hostname : partner-api.alpha.redmart.com
# Prod hostname : partner-api.redmart.com
curl --include --insecure \
     -X GET "https://{HOSTNAME}/v1/pickup-locations/{pickupLocationId}" \
     -H 'Accept: application/json' \
     -H 'Authorization: Bearer {access-token}'

```

```scala
// TODO
```

`GET /v1/pickup-locations/{pickupLocationId}`

*Querying the details of a specific pickup location*

<h3 id="getpickup-locations-pickuplocationid-parameters">Path Parameters</h3>

|Parameter|In|Required|Description|
|---|---|---|---|---|
|pickupLocationId|path|true|The unique Identifier of the pickup-location, in the response example it's the value 23789|

> 200 Response

```json
{
  "addressLine1": "1 Main Street",
  "city": "Singapore",
  "name": "ABC Merchant",
  "country": "Singapore",
  "postalCode": "123456",
  "id": "23789",
  "addressLine2": "#02-15"
}
```

> 404 Response

```json
{
  "title": "The requested resource could not be found"
}
```

<h3 id="getpickup-locations-pickuplocationid-responses">Responses</h3>

|Status|Meaning|Description|Schema|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|OK|[PickupLocation](#schemapickuplocation)|
|404|[Not Found](https://tools.ietf.org/html/rfc7231#section-6.5.4)|Not Found|[Problem](#schemaproblem)|

<h1 id="mp-partner-api-service-products">Products</h1>

The Products API allows you to retrieve Products and update their inventory stocks.

<aside class="warning">
To perform all <b>Products</b> operations, you must always send your oauth2 access-token (see the <a href="#request-a-token">Request a Token</a> section).
Look at the 'Authorization' header in the code samples.
</aside>

## Get all Products

<a id="opIdgetProducts-page-pageSize-pickupLocations"></a>

> Code samples

```shell
# Test hostname : partner-api.alpha.redmart.com
# Prod hostname : partner-api.redmart.com
curl --include --insecure \
     -X GET "https://{HOSTNAME}/v1/products?page=1&pageSize=50&pickupLocations=23789,23790" \
     -H 'Accept: application/json' \
     -H 'Authorization: Bearer {access-token}'

```

```scala
// TODO

```

`GET /v1/products`

*Query and filter products*

<h3 id="getproducts-page-pagesize-pickuplocations-parameters">Query Parameters</h3>

|Parameter|In|Type|Required|Description|
|---|---|---|---|---|
|page|query|integer|false|Page to start returning results from, must be >= 1|
|pageSize|query|integer|false|Number of items on one page, must be >= 1 and <= 100|
|pickupLocations|query|array[string]|false|The unique ids of pickup locations you want to retrieve the products from (comma-separated)|

> 200 Response

```json
{
  "page": 1,
  "pageSize": 50,
  "items": [
    {
      "barcodes": [
        "111122223333"
      ],
      "title": "Chocolate Cereals",
      "rpc": 100150,
      "pickupLocations": [
        {
          "id": "23789"
        }
      ],
      "productCode": "abc-merch-specific-choc-cereals-code"
    },
    {
      "barcodes": [
        "444455556666"
      ],
      "title": "Apple Cereals",
      "rpc": 100151,
      "pickupLocations": [
        {
          "id": "23789"
        }
      ],
      "productCode": "abc-merch-specific-choc-cereals-code"
    },
    {
      "barcodes": [
        "444455556666"
      ],
      "title": "Fresh Orange Juice",
      "rpc": 120350,
      "pickupLocations": [
        {
          "id": "23790"
        }
      ],
      "productCode": "abc-merch-specific-orange-juice-code"
    }
  ],
  "total": 3
}
```

<h3 id="getproducts-page-pagesize-pickuplocations-responses">Responses</h3>

|Status|Meaning|Description|Schema|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|OK|[Page_Product_](#schemapage_product_)|

## Get one Product

Get one Product _by RPC_ (RPC stands for 'RedMart Product Code')

<a id="opIdgetProducts-productId"></a>

> Code samples

```shell
# Test hostname : partner-api.alpha.redmart.com
# Prod hostname : partner-api.redmart.com
curl --include --insecure \
     -X GET "https://{HOSTNAME}/v1/products/{productId}" \
     -H 'Accept: application/json' \
     -H 'Authorization: Bearer {access-token}'

```

```scala
// TODO

```

`GET /v1/products/{productId}`

*Querying the details of a specific product by RPC*

<h3 id="getproducts-productid-parameters">Path Parameters</h3>

|Parameter|In|Type|Required|Description|
|---|---|---|---|---|
|productId|path|string|true|the RPC of the Product (so the RedMart-specific code, _not_ the merchant-specific code)|

> 200 Response

```json
{
  "barcodes": [
    "111122223333"
  ],
  "title": "Chocolate Cereals",
  "rpc": 100150,
  "pickupLocations": [
    {
      "id": "23789"
    }
  ],
  "productCode": "abc-merch-specific-choc-cereals-code"
}
```

> 404 Response

```json
{
  "title": "The requested resource could not be found"
}
```

<h3 id="getproducts-productid-responses">Responses</h3>

|Status|Meaning|Description|Schema|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|OK|[Product](#schemaproduct)|
|400|[Bad Request](https://tools.ietf.org/html/rfc7231#section-6.5.1)|Bad Request|[Problem](#schemaproblem)|
|404|[Not Found](https://tools.ietf.org/html/rfc7231#section-6.5.4)|Not Found|[Problem](#schemaproblem)|

## Get all Stocks

<a id="opIdgetProductsPickup-locationsStocks-productId-pickupLocationId"></a>

For now, RedMart supports only _one_ Stock per Product.

> Code samples

```shell
# Test hostname : partner-api.alpha.redmart.com
# Prod hostname : partner-api.redmart.com
curl --include --insecure \
     -X GET "https://{HOSTNAME}/v1/products/{productId}/pickup-locations/{pickupLocationId}/stocks" \
     -H 'Accept: application/json' \
     -H 'Authorization: Bearer {access-token}'

```

```scala
// TODO

```

`GET /v1/products/{productId}/pickup-locations/{pickupLocationId}/stocks`

*Querying all in-store stock levels for a specific product in a specific pickup location*

<h3 id="getproductspickup-locationsstocks-productid-pickuplocationid-parameters">Path Parameters</h3>

|Parameter|In|Type|Required|Description|
|---|---|---|---|---|
|productId|path|string|true|the RPC of the Product (so the RedMart-specific code, _not_ the merchant-specific code)|
|pickupLocationId|path|string|true|The unique id of the pickup location where the product is stored|

> 200 Response

```json
[
  {
    "availableForPickupFrom": "1970-01-01T00:00:00Z",
    "quantityAtPickupLocation": 10,
    "quantityScheduledForPickup": 2,
    "quantityAvailableForSale": 8
  }
]
```

<h3 id="getproductspickup-locationsstocks-productid-pickuplocationid-responses">Responses</h3>

|Status|Meaning|Description|Schema|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|OK|Inline|
|400|[Bad Request](https://tools.ietf.org/html/rfc7231#section-6.5.1)|Bad Request|[Problem](#schemaproblem)|
|404|[Not Found](https://tools.ietf.org/html/rfc7231#section-6.5.4)|Not Found|[Problem](#schemaproblem)|

<h3 id="getproductspickup-locationsstocks-productid-pickuplocationid-responseschema">200 Response Schema</h3>

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
||[[Stock](#schemastock)]|false|none|none|
|availableForPickupFrom|string(date-time)|true|none|Indicates from which point in time the items are available for pickup (currently unused and hardcoded to epoch 0)|
|quantityAtPickupLocation|integer(int32)|true|none|Number of items available in the pickup location|
|quantityScheduledForPickup|integer(int32)|true|none|Number of items that are scheduled for pickup in the next few days|
|quantityAvailableForSale|integer(int32)|true|none|Number of items that can currently still be ordered by customers|

## Get one Stock

<a id="opIdgetProductsPickup-locationsStocks1970-01-01T00:00:00Z-productId-pickupLocationId"></a>

For now, RedMart supports only _one_ Stock per Product.

> Code samples

```shell
# Test hostname : partner-api.alpha.redmart.com
# Prod hostname : partner-api.redmart.com
curl --include --insecure \
     -X GET "https://{HOSTNAME}/v1/products/{productId}/pickup-locations/{pickupLocationId}/stocks/1970-01-01T00:00:00Z" \
     -H 'Accept: application/json' \
     -H 'Authorization: Bearer {access-token}'

```

```scala
// TODO

```

`GET /v1/products/{productId}/pickup-locations/{pickupLocationId}/stocks/1970-01-01T00:00:00Z`

*Querying in-store stock levels for a product in a specific pickup location*

<h3 id="getproductspickup-locationsstocks1970-01-01t00:00:00z-productid-pickuplocationid-parameters">Path Parameters</h3>

|Parameter|In|Type|Required|Description|
|---|---|---|---|---|
|productId|path|string|true|the RPC of the Product (so the RedMart-specific code, _not_ the merchant-specific code)|
|pickupLocationId|path|string|true|The unique id of the pickup location where the product is stored|
|stockAvailableForPickupFrom|path|string|true|Hardcoded to epoch 0, i.e. '1970-01-01T00:00:00Z', to mean the stock is a "present" Stock (as opposed to a future/planned Stock)|

> 200 Response

```json
{
  "availableForPickupFrom": "1970-01-01T00:00:00Z",
  "quantityAtPickupLocation": 10,
  "quantityScheduledForPickup": 2,
  "quantityAvailableForSale": 8
}
```

> 404 Response

```json
{
  "title": "The requested resource could not be found"
}
```

<h3 id="getproductspickup-locationsstocks1970-01-01t00:00:00z-productid-pickuplocationid-responses">Responses</h3>

|Status|Meaning|Description|Schema|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|OK|[Stock](#schemastock)|
|400|[Bad Request](https://tools.ietf.org/html/rfc7231#section-6.5.1)|Bad Request|[Problem](#schemaproblem)|
|404|[Not Found](https://tools.ietf.org/html/rfc7231#section-6.5.4)|Not Found|[Problem](#schemaproblem)|

### Response Headers

<a id="get-one-stock-of-a-product-response-headers"></a>

|Status|Header|Type|Format|Description|
|---|---|---|---|---|
|200|ETag|string||Identifier that describes the latest state of this resource|

## Update one Stock

<a id="opIdpatchProductsPickup-locationsStocks1970-01-01T00:00:00Z-productId-pickupLocationId-If-Match-body"></a>

> Code samples

```shell
# Test hostname : partner-api.alpha.redmart.com
# Prod hostname : partner-api.redmart.com
curl --include --insecure \
     --request PATCH "https://{HOSTNAME}/v1/products/{productId}/pickup-locations/{pickupLocationId}/stocks/1970-01-01T00:00:00Z" \
     -H 'Content-Type: application/merge-patch+json' \
     -H 'Accept: application/json' \
     -H 'If-Match: 10' \
     -H 'Authorization: Bearer {access-token}' \
     --data '{"quantityAtPickupLocation": 20}'
```

```scala
// TODO

```

`PATCH /products/{productId}/pickup-locations/{pickupLocationId}/stocks/1970-01-01T00:00:00Z`

*Updating in-store stock levels for an RPC in a specific pickup location*

> Body parameter

```json
{
  "quantityAtPickupLocation": 20
}
```

<h3 id="patchproductspickup-locationsstocks1970-01-01t00:00:00z-productid-pickuplocationid-if-match-body-parameters">Path and Header Parameters</h3>

|Parameter|In|Type|Required|Description|
|---|---|---|---|---|
|productId|path|string|true|none|
|pickupLocationId|path|string|true|none|
|If-Match|header|string|true|The update request will only be processed if this value matches the latest `ETag` value of the resource (see <a href="#get-one-stock-of-a-product-response-headers">Response Headers</a> section of the _Get one Stock of a Product_ endpoint)|
|body|body|[StockUpdate](#schemastockupdate)|false|StockUpdate|

> 200 Response

```json
{
  "availableForPickupFrom": "1970-01-01T00:00:00Z",
  "quantityAtPickupLocation": 20,
  "quantityScheduledForPickup": 2,
  "quantityAvailableForSale": 18
}
```

> 412 Response

```json
{
  "title": "the If-Match condition evaluated to false"
 }
```


<h3 id="patchproductspickup-locationsstocks1970-01-01t00:00:00z-productid-pickuplocationid-if-match-body-responses">Responses</h3>

|Status|Meaning|Description|Schema|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|OK|[Stock](#schemastock)|
|400|[Bad Request](https://tools.ietf.org/html/rfc7231#section-6.5.1)|Bad Request|[Problem](#schemaproblem)|
|404|[Not Found](https://tools.ietf.org/html/rfc7231#section-6.5.4)|Not Found|[Problem](#schemaproblem)|
|412|[Precondition Failed](https://tools.ietf.org/html/rfc7232#section-4.2)|Precondition Failed|[Problem](#schemaproblem)|

### Response Headers

|Status|Header|Type|Format|Description|
|---|---|---|---|---|
|200|ETag|string||Identifier that describes the latest state of this resource|

# _Schemas_

All schemas referenced in the APIs documented above 

<h2 id="tocSpickuplocation">PickupLocation</h2>

<a id="schemapickuplocation"></a>

```json
{
  "addressLine1": "string",
  "city": "string",
  "name": "string",
  "country": "string",
  "postalCode": "string",
  "id": "string",
  "addressLine2": "string"
}

```

*PickupLocation*

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|addressLine1|string|true|none|none|
|city|string|true|none|none|
|name|string|true|none|none|
|country|string|true|none|none|
|postalCode|string|true|none|none|
|id|string|true|none|none|
|addressLine2|string|true|none|none|

<h2 id="tocSstockupdate">StockUpdate</h2>

<a id="schemastockupdate"></a>

```json
{
  "quantityAtPickupLocation": 0
}

```

*StockUpdate*

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|quantityAtPickupLocation|integer(int32)|true|none|Number of items available in the pickup location|

<h2 id="tocSproblem">Problem</h2>

<a id="schemaproblem"></a>

```json
{
  "title": "string"
}

```

*Problem*

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|title|string|true|none|none|

<h2 id="tocSpickuplocationref">PickupLocationRef</h2>

<a id="schemapickuplocationref"></a>

```json
{
  "id": "string"
}

```

*PickupLocationRef*

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|id|string|true|none|none|

<h2 id="tocSproduct">Product</h2>

<a id="schemaproduct"></a>

```json
{
  "barcodes": [
    "string"
  ],
  "title": "string",
  "rpc": 0,
  "pickupLocations": [
    {
      "addressLine1": "string",
      "city": "string",
      "name": "string",
      "country": "string",
      "postalCode": "string",
      "id": "string",
      "addressLine2": "string"
    }
  ],
  "productCode": "string"
}

```

*Product*

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|barcodes|[string]|true|none|none|
|title|string|true|none|none|
|rpc|integer(int64)|true|none|The RPC (RedMart Product Code) of an item|
|pickupLocations|[[PickupLocation](#schemapickuplocation)]|true|none|none|
|productCode|string|true|none|Custom product code as defined by the seller|

<h2 id="tocSstock">Stock</h2>

<a id="schemastock"></a>

```json
{
  "availableForPickupFrom": "2018-07-03T04:25:53Z",
  "quantityAtPickupLocation": 0,
  "quantityScheduledForPickup": 0,
  "quantityAvailableForSale": 0
}

```

*Stock*

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|availableForPickupFrom|string(date-time)|true|none|Indicates from which point in time the items are available for pickup (currently unused)|
|quantityAtPickupLocation|integer(int32)|true|none|Number of items available in the pickup location|
|quantityScheduledForPickup|integer(int32)|true|none|Number of items that are scheduled for pickup in the next few days|
|quantityAvailableForSale|integer(int32)|true|none|Number of items that can currently still be ordered by customers|

<h2 id="tocSpage_pickuplocation_">Page_PickupLocation_</h2>

<a id="schemapage_pickuplocation_"></a>

```json
{
  "page": 0,
  "pageSize": 0,
  "items": [
    {
      "addressLine1": "string",
      "city": "string",
      "name": "string",
      "country": "string",
      "postalCode": "string",
      "id": "string",
      "addressLine2": "string"
    }
  ],
  "total": 0
}

```

*Page«PickupLocation»*

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|page|integer(int32)|true|none|none|
|pageSize|integer(int32)|true|none|none|
|items|[[PickupLocation](#schemapickuplocation)]|true|none|none|
|total|integer(int32)|false|none|none|

<h2 id="tocSpage_product_">Page_Product_</h2>

<a id="schemapage_product_"></a>

```json
{
  "page": 0,
  "pageSize": 0,
  "items": [
    {
      "barcodes": [
        "string"
      ],
      "title": "string",
      "rpc": 0,
      "pickupLocations": [
        {
          "addressLine1": "string",
          "city": "string",
          "name": "string",
          "country": "string",
          "postalCode": "string",
          "id": "string",
          "addressLine2": "string"
        }
      ],
      "productCode": "string"
    }
  ],
  "total": 0
}

```

*Page«Product»*

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|page|integer(int32)|true|none|none|
|pageSize|integer(int32)|true|none|none|
|items|[[Product](#schemaproduct)]|true|none|none|
|total|integer(int32)|false|none|none|
