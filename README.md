# HyperVerge Face Matching - API Documentation

## Overview

This documentation describes the Face Matching API v1. If you have any queries please contact support. The postman collection can be found at this [link](https://www.getpostman.com/collections/7c7727ceda7ebe3bb7ce).

## Table Of Contents

- [HyperVerge Face Matching - API Documentation](#hyperverge-face-matching-api-documentation)
	- [Overview](#overview)
	- [Table Of Contents](#table-of-contents)
	- [Schema](#schema)
	- [Parameters](#parameters)
	- [Root Endpoint](#root-endpoint)
	- [Authentication](#authentication)
	- [Media Types](#media-types)
	- [API Endpoint](#api-endpoint)
	- [Data logging and clientId](#data-logging-and-clientid)

## Schema

Currently, HTTP and HTTPS are supported. All data is received as JSON, and all image uploads are to be performed as form-data (POST request).
It is recommended that HTTPS is used for all API calls. For HTTPS, only TLS v1.2 is supported to ensure better security.

## Parameters
All optional and compulsory parameters are passed as part of the request body.

## Root Endpoint
A `GET` request can be issued to the root endpoint to check for successful connection. Users in the Asia Pacific region can use the URL given below. The actual API URL for other geographies will be provided later.

	 curl https://apac.docs.hyperverge.co/v1/

The `plain/text` reponse of `Hello!` should be received.

## Authentication

Currently, an appId, appKey combination is passed in the request header. The appId and appKey are provided on request by the HyperVerge team. If you would like to try the API, please reach out to contact@hyperverge.co

	curl -X POST https://apac.docs.hyperverge.co/v1/photo/verifyPair\
	  -H 'appid: xxx' \
	  -H 'appkey: yyy' \
	  -H 'content-type: multipart/form-data;' \
	  -F 'image1=@abc.png' \
	  -F 'image2=@def.png' \
	  -F 'type=id'


On failed attempt with invalid credentials or unauthorized access the HTTP response would have a status code : 401

Please do not expose the appid and appkey on browser applications. In case of a browser application, set up the API calls from the server side.

## Media Types

Currently, `jpeg, png and tiff` images are supported by the API. The image can be passed as url or uploaded.


## API Endpoint

Used to verify if the 2 face images belong to the same person

* **URL**

  - /photo/verifyPair

* **Method:**

    `POST`

* **Header**

	- content-type : 'formdata'
	- appid
	- appkey

* **Request Body**

    - type : use 'id' for Selfie to ID card / Application photo matching and 'selfie' for Selfie to Selfie matching
	- image1, image2
	or
	- imageUrls
* **Success Response:**

  * **Code:** 200 <br />
  * Incase of a properly made request, the response would follow schema.


		```
		{
			"status" : "success",
			"statusCode" : "200",
			"result" : {
                "match" : "yes/no",
                "match-score" : 95, // 0-100
                "to-be-reviewed": "yes/no",
				"match_score": 95 // 0-100; to be deprecated : it is same as match-score
			}
		}
		```

	* Incase of a properly made request, the response would follow schema if the image does not contain a detectable face.



		```
		{
			"status" : "success",
			"statusCode" : "200",
			"result" : {
				"error" : "Face not detected in one or more images"
			}
		}
		```


* **Error Response:**

There are 3 types of request errors and `HTTP Status Code 400` is returned in all 3 cases:

1. No Image or 1 image input

		{
		    "status": "failure",
		    "statusCode": "400",
		    "error": "image1 and image2 are required files"
		}

1. type not specified

		{
		    "status": "failure",
		    "statusCode": "400",
		    "error": "type is a required parameter, supported types are ['id', 'selfie']"
		}

3. Larger than allowed image input

		{
		  "status": "failure",
		  "statusCode": "400",
		  "error": "image size cannot be greater than 6MB"
		}

All error messages follow the same syntax with the statusCode and status also being a part of the response body, and `string` error message with the description of the error.

**Server Errors**
We try our best to avoid these errors, but if by chance they do occur the response code will be 5xx.

1. Form parse error or bad form-data

     ```
    {
         "status": "failure",
         "statusCode": "500",
         "error": "upload error"
    }
    ```
In this scenario please ensure that the form-data is correctly created.

* **Sample Calls:**

    ```
    curl -X POST https://apac.docs.hyperverge.co/v1/photo/verifyPair \
		  -H 'appid: xxx' \
		  -H 'appkey: yyyy' \
		  -H 'content-type: multipart/form-data;\
          -F 'type=selfie'\
		  -F 'image1=@image_1_path.png' \
		  -F 'image2=@image_2_path.png'
    ```
    
## Data logging and clientId

These optional params are used to facilitate better debugging of the system. 

`clientId` is a unique identifier that is assigned to the end customer by the API user. This would need to be passed in the request body. And the parameter, would be the 
same for the different API calls made for the of same customer.

By default, the input images are not stored by HyperVerge systems, however, if the user sets the optional parameter `dataLogging` to string value "yes", then the images will be stored and the requestId can be 
provided to HyperVerge to check the uploaded image incase of an inaccurate extraction.
