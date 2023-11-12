---
title: Oakra API Reference

toc_footers:
  - <a href='https://app.oakra.com/login/'>Sign in to Oakra</a>

code_clipboard: true
---

# Authentication

All requests to the Oakra API are authenticated using HTTP bearer authentication. Include your App Key in the header of every API request using the Authorization header:

`Authorization: Bearer <your_app_key>`

# Versioning

Every API request must specify an API version in its headers. Currently all requests should use version `v1`.

`X-OAKRA-API-VERSION: v1`


# Stock


## Get stock records

> Request

```shell
curl -X GET "http://app.oakra.com/api/v1/stock/stock-records/?location_id=warehouse-1&sku=shirt-blue-m&&sku=shirt-green-m" \
  -H 'Authorization: Bearer bd0bbbaa0cf25202981e7542b02ce1af3545b7102e96d6020b1e91c67fee159f'
  -H 'X-OAKRA-API-VERSION: v1'

```

> Response

```json
[
  {
    "id": 122,
    "location_id": "warehouse-1",
    "sku": "shirt-blue-m",
    "quantity": 135
  },
  {
    "id": 123,
    "location_id": "warehouse-1",
    "sku": "shirt-green-m",
    "quantity": 142
  }
]
```

Stock records represent a physical item at a specific location.

### Endpoint

`GET http://app.oakra.com/api/v1/stock/stock-records/`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
sku | Yes | The SKU of the inventory item. You can provide multiple values.
location_id | No | The code name of the location where the inventory is located, i.e warehouse-1


### Response

Parameter | Type | Description
--------- | ---- | -----------
stock_records | array |
→ id | int | The ID of the stock record
→ location_id | string | The code name of the location where the inventory is located
→ sku | string | The SKU of the inventory item
→ quantity | int | The number of units in stock of the item


## Update stock record

> Request

```shell
curl -X PATCH "http://app.oakra.com/api/v1/stock/stock-record/" \
  -H 'Authorization: Bearer bd0bbbaa0cf25202981e7542b02ce1af3545b7102e96d6020b1e91c67fee159f'
  -H 'X-OAKRA-API-VERSION: v1'
  -d '{
        "stock_records": [
            {
              "id": 123,
              "quantity": 150
            }
          ]
      }'

```

> Response

```json
{
  "stock_records": [
    {
      "id": 123,
      "location_id": "warehouse-1",
      "sku": "shirt-green-m",
      "quantity": 150
    }
  ]
}
```

Use this endpoint to notify Oakra how many units of a stock record is now available.

### Endpoint

`POST http://app.oakra.com/api/v1/stock/stock-record/update/`

### JSON Body

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
stock_records | array | Yes |
→ id | int | Yes | The id of stock record. Lookup using the `stock/stock-records/` endpoint
→ quantity | int | Yes | The number of units of the item currently available at the specified location

### Response


Parameter | Type | Description
--------- | ---- | -----------
stock_records | array |
→ id | int | The ID of the stock record
→ location_id | string | The code name of the location where the inventory is located
→ sku | string | The SKU of the inventory item
→ quantity | int | The number of units in stock of the item


## Change in stock

> Request

```shell
curl -X POST "http://app.oakra.com/api/v1/stock/stock-record/change/" \
  -H 'Authorization: Bearer bd0bbbaa0cf25202981e7542b02ce1af3545b7102e96d6020b1e91c67fee159f'
  -H 'X-OAKRA-API-VERSION: v1'
  -d '{
        "stock_records": [
          {
            "id": 123,
            "quanity_change": 20
          }
        ]
      }'

```

> Response

```json
{
  "stock_records": [
    {
      "id": 123,
      "location_id": "warehouse-1",
      "sku": "shirt-green-m",
      "quantity": 170
    }
  ]
}
```

Use this endpoint to notify Oakra that the number of units of an inventory item has changed.

This differs from [`/stock/stock-record/update/`]('#update-stock-record') in that it expects the *change* in the number of units at the location, as opposed to the actual number of units available at the location.

In most cases, this endpoint is preferred for updating stock quantity, since it avoids race conditions that are possible with `/stock/stock-record/`.

### Endpoint

`POST http://app.oakra.com/api/v1/stock/stock-records/change/`

### JSON Body

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
stock_records | array | Yes |
→ id | int | Yes | The id of stock record. Lookup using the `stock/stock-records/` endpoint
→ quantity_change | int | Yes | The number of units that have been added or deducted from stock of the item. Units added should be represented by a positive integer; units deducted should be represented by a negative integer.


### Response


Parameter | Type | Description
--------- | ---- | -----------
stock_records | array |
→ id | int | The ID of the stock record
→ location_id | string | The code name of the location where the inventory is located
→ sku | string | The SKU of the inventory item
→ quantity | int | The number of units in stock of the item