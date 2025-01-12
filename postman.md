---

copyright:
  years: 2017, 2022
lastupdated: "2022-01-19"

keywords: rest, s3, compatibility, api, postman, client, object storage

subcollection: cloud-object-storage


---

{{site.data.keyword.attribute-definition-list}}


# Using `Postman`
{: #postman}

Here's a basic `Postman` setup for the {{site.data.keyword.cos_full}} REST API. More detail can be found in the API reference for [buckets](/docs/services/cloud-object-storage/api-reference?topic=cloud-object-storage-compatibility-api-bucket-operations) or [objects](/docs/services/cloud-object-storage?topic=cloud-object-storage-object-operations).

Using `Postman` assumes a certain amount of familiarity with Object Storage and the necessary information from a [service credential](/docs/services/cloud-object-storage/iam?topic=cloud-object-storage-service-credentials) or the console as shown in the [getting started with {{site.data.keyword.cos_full_notm}}](/docs/cloud-object-storage?topic=cloud-object-storage-getting-started-cloud-object-storage). If any terms or variables are unfamiliar, they can be found in the [FAQ](/docs/cloud-object-storage?topic=cloud-object-storage-faq).

Personally Identifiable Information (PII): When _naming_ buckets or objects, do not use any information that can identify any user (natural person) by name, location, or any other means. 
{:tip}

## REST API client overview
{: #postman-rest}

Interacting with a REST API isn't as simple as using a standard internet browser. Simple browsers do not allow any manipulation of the URL request. A REST API client can help quickly put together both simple and complex HTTP requests.  

## Prerequisites
{: #postman-prereqs}
* IBM Cloud account
* [Cloud Storage resource created](https://cloud.ibm.com/catalog/){: external} (lite plan works fine)
* [IBM Cloud COS CLI installed and configured](/docs/cloud-object-storage?topic=cloud-object-storage-cli-plugin-ic-cos-cli)
* [Service Instance ID for your Cloud Storage](/docs/services/cloud-object-storage?topic=cloud-object-storage-service-credentials#service-credentials)
* [IAM (Identity and Access Management) Token](/docs/services/cloud-object-storage?topic=cloud-object-storage-service-credentials#service-credentials) 
* [Endpoint for your COS bucket](/docs/services/cloud-object-storage/basics?topic=cloud-object-storage-endpoints)

### Create a bucket
{: #postman-create-bucket}
1.	Start Postman
2.	In the **New** tab, select `PUT`.
3.	Enter the endpoint in the address bar and add the name for your new bucket.
a.	Bucket names must be unique across all buckets, so choose something specific.
4.	In the **Type** menu, select Bearer Token.
5.	Add the IAM Token in the Token box.
6.	Click **Preview Request**.
a.	You should see a confirmation message that the headers were added.
7.	Click the **Header** tab where you should see an existing entry for Authorization.
8.	Add a key.
a.	Key: `ibm-service-instance-id`
b.	Value: Resource Instance ID for your cloud storage service.
9.	Click Send.
10.	You'll receive a status `200 OK` message.

### Create a text file object
{: #postman-create-text-file}

1.	Create a tab by clicking the Plus (+) icon.
2.	Select `PUT` from the list.
3.	In the address bar, enter the endpoint address with the bucket name from previous section and a file name.
4.	In the Type list select Bearer Token.
5.	Add the IAM Token in the token box.
6.	Select the Body tab.
7.	Select raw option and ensure that Text is selected.
8.	Enter text in the provided space.
9.	Click Send.
10.	You receive a status `200 OK` message.

### List the contents of a bucket
{: #postman-list-objects}

1.	Create a new tab by selecting the Plus (+) icon.
2.	Verify `GET` is selected in the list.
3.	In the address bar, enter the endpoint address with the bucket name from the previous section.
4.	In the Type list, select Bearer Token.
5.	Add the IAM Token in the token box.
6.	Click Send.
7.	You receive a status `200 OK` message.
8.	In the Body of the Response section is an XML message with the list of files in your bucket.

## Using the sample collection
{: #postman-collection}

This Postman collection is provided as a starting point for experimenting with the API, and it not intended for production use:

```json
{
	"info": {
		"_postman_id": "56d99641-9ad6-4218-b3d4-18ac8f3361e0",
		"name": "IBM COS",
		"description": "IBM COS samples",
		"schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
	},
	"item": [
		{
			"name": "Retrieve list of buckets",
			"event": [
				{
					"listen": "test",
					"script": {
						"id": "d67afcf2-6d35-4a2a-9542-b8d5df78eded",
						"type": "text/javascript",
						"exec": [
							"pm.test(\"Request was successful\", function() {",
							"   pm.response.to.be.success; ",
							"});",
							"",
							"pm.test(\"Response contains expected content\", function() {",
							"    pm.expect(pm.response.text()).to.include(\"ListAllMyBucketsResult\");",
							"});"
						]
					}
				}
			],
			"request": {
				"auth": {
					"type": "bearer",
					"bearer": [
						{
							"key": "token",
							"value": "{{iamtoken}}",
							"type": "string"
						}
					]
				},
				"method": "GET",
				"header": [
					{
						"key": "ibm-service-instance-id",
						"value": "{{serviceid}}"
					}
				],
				"body": {},
				"url": {
					"raw": "https://{{endpoint-region}}cloud-object-storage.appdomain.cloud",
					"protocol": "https",
					"host": [
						"{{endpoint-region}}",
						"objectstorage",
						"softlayer",
						"net"
					]
				}
			},
			"response": []
		},
		{
			"name": "Create new bucket",
			"event": [
				{
					"listen": "test",
					"script": {
						"id": "3cebb9d7-90ee-42c0-9154-b26bd229e179",
						"type": "text/javascript",
						"exec": [
							"pm.test(\"Request was successful\", function() {",
							"   pm.response.to.be.success; ",
							"});"
						]
					}
				}
			],
			"request": {
				"auth": {
					"type": "bearer",
					"bearer": [
						{
							"key": "token",
							"value": "{{iamtoken}}",
							"type": "string"
						}
					]
				},
				"method": "PUT",
				"header": [
					{
						"key": "ibm-service-instance-id",
						"value": "{{serviceid}}"
					}
				],
				"body": {
					"mode": "raw",
					"raw": ""
				},
				"url": {
					"raw": "https://{{endpoint-region}}cloud-object-storage.appdomain.cloud/{{bucket}}",
					"protocol": "https",
					"host": [
						"{{endpoint-region}}",
						"objectstorage",
						"softlayer",
						"net"
					],
					"path": [
						"{{bucket}}"
					]
				},
				"description": "Create new bucket"
			},
			"response": []
		},
		{
			"name": "Create new text file",
			"event": [
				{
					"listen": "test",
					"script": {
						"id": "0a54c09b-0032-4933-ae99-81911935cb4d",
						"type": "text/javascript",
						"exec": [
							"pm.test(\"Request was successful\", function() {",
							"   pm.response.to.be.success; ",
							"});",
							"",
							"pm.test(\"Response contains expected header\", function() {",
							"    pm.response.to.have.header(\"ETag\");",
							"});"
						]
					}
				}
			],
			"request": {
				"auth": {
					"type": "bearer",
					"bearer": [
						{
							"key": "token",
							"value": "{{iamtoken}}",
							"type": "string"
						}
					]
				},
				"method": "PUT",
				"header": [
					{
						"key": "Content-Type",
						"value": "application/x-www-form-urlencoded"
					}
				],
				"body": {
					"mode": "raw",
					"raw": "This is test data for the text file."
				},
				"url": {
					"raw": "https://{{endpoint-region}}cloud-object-storage.appdomain.cloud/{{bucket}}/testfile.txt",
					"protocol": "https",
					"host": [
						"{{endpoint-region}}",
						"objectstorage",
						"softlayer",
						"net"
					],
					"path": [
						"{{bucket}}",
						"testfile.txt"
					]
				},
				"description": "Create a new text file in the bucket"
			},
			"response": []
		},
		{
			"name": "Create new binary file",
			"event": [
				{
					"listen": "test",
					"script": {
						"id": "80e3e8bd-3fb5-4874-a638-510cf5b8c872",
						"type": "text/javascript",
						"exec": [
							"pm.test(\"Request was successful\", function() {",
							"   pm.response.to.be.success; ",
							"});",
							"",
							"pm.test(\"Response contains expected header\", function() {",
							"    pm.response.to.have.header(\"ETag\");",
							"});"
						]
					}
				}
			],
			"request": {
				"auth": {
					"type": "bearer",
					"bearer": [
						{
							"key": "token",
							"value": "{{iamtoken}}",
							"type": "string"
						}
					]
				},
				"method": "PUT",
				"header": [
					{
						"key": "Content-Type",
						"value": "image/jpeg"
					}
				],
				"body": {
					"mode": "file",
					"file": {}
				},
				"url": {
					"raw": "https://{{endpoint-region}}cloud-object-storage.appdomain.cloud/{{bucket}}/testimage.jpg",
					"protocol": "https",
					"host": [
						"{{endpoint-region}}",
						"objectstorage",
						"softlayer",
						"net"
					],
					"path": [
						"{{bucket}}",
						"testimage.jpg"
					]
				},
				"description": "Create a new binary (image) file in the bucket"
			},
			"response": []
		},
		{
			"name": "Retrieve list of files from bucket",
			"event": [
				{
					"listen": "test",
					"script": {
						"id": "aae11dfd-89e5-464d-a6da-44c05652ccec",
						"type": "text/javascript",
						"exec": [
							"pm.test(\"Request was successful\", function() {",
							"   pm.response.to.be.success; ",
							"});",
							"",
							"pm.test(\"Response contains expected content\", function() {",
							"    pm.expect(pm.response.text()).to.include(\"ListBucketResult\");",
							"});"
						]
					}
				}
			],
			"request": {
				"auth": {
					"type": "bearer",
					"bearer": [
						{
							"key": "token",
							"value": "{{iamtoken}}",
							"type": "string"
						}
					]
				},
				"method": "GET",
				"header": [],
				"body": {
					"mode": "raw",
					"raw": ""
				},
				"url": {
					"raw": "https://{{endpoint-region}}cloud-object-storage.appdomain.cloud/{{bucket}}",
					"protocol": "https",
					"host": [
						"{{endpoint-region}}",
						"objectstorage",
						"softlayer",
						"net"
					],
					"path": [
						"{{bucket}}"
					]
				},
				"description": "Retrieve the list of files available in the bucket"
			},
			"response": []
		},
		{
			"name": "Retrieve list of files from bucket (filter by prefix)",
			"event": [
				{
					"listen": "test",
					"script": {
						"id": "aae11dfd-89e5-464d-a6da-44c05652ccec",
						"type": "text/javascript",
						"exec": [
							"pm.test(\"Request was successful\", function() {",
							"   pm.response.to.be.success; ",
							"});",
							"",
							"pm.test(\"Response contains expected content\", function() {",
							"    pm.expect(pm.response.text()).to.include(\"ListBucketResult\");",
							"});"
						]
					}
				}
			],
			"request": {
				"auth": {
					"type": "bearer",
					"bearer": [
						{
							"key": "token",
							"value": "{{iamtoken}}",
							"type": "string"
						}
					]
				},
				"method": "GET",
				"header": [],
				"body": {
					"mode": "raw",
					"raw": ""
				},
				"url": {
					"raw": "https://{{endpoint-region}}cloud-object-storage.appdomain.cloud/{{bucket}}?prefix=new",
					"protocol": "https",
					"host": [
						"{{endpoint-region}}",
						"objectstorage",
						"softlayer",
						"net"
					],
					"path": [
						"{{bucket}}"
					],
					"query": [
						{
							"key": "prefix",
							"value": "new"
						}
					]
				},
				"description": "Retrieve the list of files available in the bucket"
			},
			"response": []
		},
		{
			"name": "Retrieve text file",
			"event": [
				{
					"listen": "test",
					"script": {
						"id": "58817cc7-7d15-45a9-b372-7712d2fd389d",
						"type": "text/javascript",
						"exec": [
							"pm.test(\"Request was successful\", function() {",
							"   pm.response.to.be.success; ",
							"});",
							"",
							"pm.test(\"Response contains expected body content\", function() {",
							"    pm.expect(pm.response.text()).to.include(\"This is test data\");",
							"});",
							"",
							"pm.test(\"Response contains expected header\", function() {",
							"    pm.response.to.have.header(\"ETag\");",
							"});"
						]
					}
				}
			],
			"request": {
				"auth": {
					"type": "bearer",
					"bearer": [
						{
							"key": "token",
							"value": "{{iamtoken}}",
							"type": "string"
						}
					]
				},
				"method": "GET",
				"header": [],
				"body": {
					"mode": "raw",
					"raw": ""
				},
				"url": {
					"raw": "https://{{endpoint-region}}cloud-object-storage.appdomain.cloud/{{bucket}}/testfile.txt",
					"protocol": "https",
					"host": [
						"{{endpoint-region}}",
						"objectstorage",
						"softlayer",
						"net"
					],
					"path": [
						"{{bucket}}",
						"testfile.txt"
					]
				},
				"description": "Retrieving a file from bucket"
			},
			"response": []
		},
		{
			"name": "Retrieve binary file",
			"event": [
				{
					"listen": "test",
					"script": {
						"id": "db6fa324-080a-43bc-a301-32e70d9bbd65",
						"type": "text/javascript",
						"exec": [
							"pm.test(\"Request was successful\", function() {",
							"   pm.response.to.be.success; ",
							"});",
							"",
							"pm.test(\"Response contains expected header\", function() {",
							"    pm.response.to.have.header(\"ETag\");",
							"});"
						]
					}
				}
			],
			"request": {
				"auth": {
					"type": "bearer",
					"bearer": [
						{
							"key": "token",
							"value": "{{iamtoken}}",
							"type": "string"
						}
					]
				},
				"method": "GET",
				"header": [],
				"body": {
					"mode": "raw",
					"raw": ""
				},
				"url": {
					"raw": "https://{{endpoint-region}}cloud-object-storage.appdomain.cloud/{{bucket}}/testimage.jpg",
					"protocol": "https",
					"host": [
						"{{endpoint-region}}",
						"objectstorage",
						"softlayer",
						"net"
					],
					"path": [
						"{{bucket}}",
						"testimage.jpg"
					]
				},
				"description": "Retrieve a binary file from the bucket"
			},
			"response": []
		},
		{
			"name": "Retrieve list of failed multipart uploads",
			"event": [
				{
					"listen": "test",
					"script": {
						"id": "835bcaf6-6e7d-4fa9-883e-1a5c5538ac7e",
						"type": "text/javascript",
						"exec": [
							"pm.test(\"Request was successful\", function() {",
							"   pm.response.to.be.success; ",
							"});",
							"",
							"pm.test(\"Response contains expected content\", function() {",
							"    pm.expect(pm.response.text()).to.include(\"ListMultipartUploadsResult\");",
							"});"
						]
					}
				}
			],
			"request": {
				"auth": {
					"type": "bearer",
					"bearer": [
						{
							"key": "token",
							"value": "{{iamtoken}}",
							"type": "string"
						}
					]
				},
				"method": "GET",
				"header": [],
				"body": {
					"mode": "raw",
					"raw": ""
				},
				"url": {
					"raw": "https://{{endpoint-region}}cloud-object-storage.appdomain.cloud/{{bucket}}?uploads=",
					"protocol": "https",
					"host": [
						"{{endpoint-region}}",
						"objectstorage",
						"softlayer",
						"net"
					],
					"path": [
						"{{bucket}}"
					],
					"query": [
						{
							"key": "uploads",
							"value": ""
						}
					]
				},
				"description": "Retrieve the list of files available in the bucket"
			},
			"response": []
		},
		{
			"name": "Retrieve list of failed multipart uploads (filter by name)",
			"event": [
				{
					"listen": "test",
					"script": {
						"id": "835bcaf6-6e7d-4fa9-883e-1a5c5538ac7e",
						"type": "text/javascript",
						"exec": [
							"pm.test(\"Request was successful\", function() {",
							"   pm.response.to.be.success; ",
							"});",
							"",
							"pm.test(\"Response contains expected content\", function() {",
							"    pm.expect(pm.response.text()).to.include(\"ListMultipartUploadsResult\");",
							"});"
						]
					}
				}
			],
			"request": {
				"auth": {
					"type": "bearer",
					"bearer": [
						{
							"key": "token",
							"value": "{{iamtoken}}",
							"type": "string"
						}
					]
				},
				"method": "GET",
				"header": [],
				"body": {
					"mode": "raw",
					"raw": ""
				},
				"url": {
					"raw": "https://{{endpoint-region}}cloud-object-storage.appdomain.cloud/{{bucket}}?uploads=&prefix=my",
					"protocol": "https",
					"host": [
						"{{endpoint-region}}",
						"objectstorage",
						"softlayer",
						"net"
					],
					"path": [
						"{{bucket}}"
					],
					"query": [
						{
							"key": "uploads",
							"value": ""
						},
						{
							"key": "prefix",
							"value": "my"
						}
					]
				},
				"description": "Retrieve the list of files available in the bucket"
			},
			"response": []
		},
		{
			"name": "Set CORS enabled bucket",
			"event": [
				{
					"listen": "test",
					"script": {
						"id": "5cf3d531-13f1-4cf6-a4fc-2f8d2d8e48af",
						"type": "text/javascript",
						"exec": [
							"pm.test(\"Request was successful\", function() {",
							"   pm.response.to.be.success; ",
							"});"
						]
					}
				}
			],
			"request": {
				"auth": {
					"type": "bearer",
					"bearer": [
						{
							"key": "token",
							"value": "{{iamtoken}}",
							"type": "string"
						}
					]
				},
				"method": "PUT",
				"header": [
					{
						"key": "ibm-service-instance-id",
						"value": "{{serviceid}}"
					},
					{
						"key": "Content-MD5",
						"value": "GQmpTNpruOyK6YrxHnpj7g=="
					}
				],
				"body": {
					"mode": "raw",
					"raw": "<CORSConfiguration>\n  <CORSRule>\n    <AllowedOrigin>http:www.ibm.com</AllowedOrigin>\n    <AllowedMethod>GET</AllowedMethod>\n    <AllowedMethod>PUT</AllowedMethod>\n    <AllowedMethod>POST</AllowedMethod>\n  </CORSRule>\n</CORSConfiguration>"
				},
				"url": {
					"raw": "https://{{endpoint-region}}cloud-object-storage.appdomain.cloud/{{bucket}}?cors=",
					"protocol": "https",
					"host": [
						"{{endpoint-region}}",
						"objectstorage",
						"softlayer",
						"net"
					],
					"path": [
						"{{bucket}}"
					],
					"query": [
						{
							"key": "cors",
							"value": ""
						}
					]
				}
			},
			"response": []
		},
		{
			"name": "Retrieve bucket CORS config",
			"event": [
				{
					"listen": "test",
					"script": {
						"id": "d9c0dce3-decd-4f3e-b5b1-e3f4a89f1283",
						"type": "text/javascript",
						"exec": [
							"pm.test(\"Request was successful\", function() {",
							"   pm.response.to.be.success; ",
							"});",
							"",
							"pm.test(\"Response contains expected content\", function() {",
							"    pm.expect(pm.response.text()).to.include(\"CORSConfiguration\");",
							"});"
						]
					}
				}
			],
			"request": {
				"auth": {
					"type": "bearer",
					"bearer": [
						{
							"key": "token",
							"value": "{{iamtoken}}",
							"type": "string"
						}
					]
				},
				"method": "GET",
				"header": [],
				"body": {
					"mode": "raw",
					"raw": ""
				},
				"url": {
					"raw": "https://{{endpoint-region}}cloud-object-storage.appdomain.cloud/{{bucket}}?cors=",
					"protocol": "https",
					"host": [
						"{{endpoint-region}}",
						"objectstorage",
						"softlayer",
						"net"
					],
					"path": [
						"{{bucket}}"
					],
					"query": [
						{
							"key": "cors",
							"value": ""
						}
					]
				},
				"description": "Retrieve the list of files available in the bucket"
			},
			"response": []
		},
		{
			"name": "Delete bucket CORS config",
			"event": [
				{
					"listen": "test",
					"script": {
						"id": "83ce0bed-572f-4c16-b45f-67a7a9cc5550",
						"type": "text/javascript",
						"exec": [
							"pm.test(\"Request was successful\", function() {",
							"   pm.response.to.be.success; ",
							"});"
						]
					}
				}
			],
			"request": {
				"auth": {
					"type": "bearer",
					"bearer": [
						{
							"key": "token",
							"value": "{{iamtoken}}",
							"type": "string"
						}
					]
				},
				"method": "DELETE",
				"header": [],
				"body": {
					"mode": "raw",
					"raw": ""
				},
				"url": {
					"raw": "https://{{endpoint-region}}cloud-object-storage.appdomain.cloud/{{bucket}}?cors=",
					"protocol": "https",
					"host": [
						"{{endpoint-region}}",
						"objectstorage",
						"softlayer",
						"net"
					],
					"path": [
						"{{bucket}}"
					],
					"query": [
						{
							"key": "cors",
							"value": ""
						}
					]
				},
				"description": "Retrieve the list of files available in the bucket"
			},
			"response": []
		},
		{
			"name": "Delete text file",
			"event": [
				{
					"listen": "test",
					"script": {
						"id": "78b76a84-9562-4692-9634-e10912574a89",
						"type": "text/javascript",
						"exec": [
							"pm.test(\"Request was successful\", function() {",
							"   pm.response.to.be.success; ",
							"});",
							""
						]
					}
				}
			],
			"request": {
				"auth": {
					"type": "bearer",
					"bearer": [
						{
							"key": "token",
							"value": "{{iamtoken}}",
							"type": "string"
						}
					]
				},
				"method": "DELETE",
				"header": [],
				"body": {
					"mode": "raw",
					"raw": ""
				},
				"url": {
					"raw": "https://{{endpoint-region}}cloud-object-storage.appdomain.cloud/{{bucket}}/testfile.txt",
					"protocol": "https",
					"host": [
						"{{endpoint-region}}",
						"objectstorage",
						"softlayer",
						"net"
					],
					"path": [
						"{{bucket}}",
						"testfile.txt"
					]
				},
				"description": "Retrieving a file from bucket"
			},
			"response": []
		},
		{
			"name": "Delete binary file",
			"event": [
				{
					"listen": "test",
					"script": {
						"id": "a8363887-2b04-4deb-a75b-10220d30e856",
						"type": "text/javascript",
						"exec": [
							"pm.test(\"Request was successful\", function() {",
							"   pm.response.to.be.success; ",
							"});"
						]
					}
				}
			],
			"request": {
				"auth": {
					"type": "bearer",
					"bearer": [
						{
							"key": "token",
							"value": "{{iamtoken}}",
							"type": "string"
						}
					]
				},
				"method": "DELETE",
				"header": [],
				"body": {
					"mode": "raw",
					"raw": ""
				},
				"url": {
					"raw": "https://{{endpoint-region}}cloud-object-storage.appdomain.cloud/{{bucket}}/testimage.jpg",
					"protocol": "https",
					"host": [
						"{{endpoint-region}}",
						"objectstorage",
						"softlayer",
						"net"
					],
					"path": [
						"{{bucket}}",
						"testimage.jpg"
					]
				},
				"description": "Retrieve a binary file from the bucket"
			},
			"response": []
		},
		{
			"name": "Delete bucket",
			"event": [
				{
					"listen": "test",
					"script": {
						"id": "3cebb9d7-90ee-42c0-9154-b26bd229e179",
						"type": "text/javascript",
						"exec": [
							"pm.test(\"Request was successful\", function() {",
							"   pm.response.to.be.success; ",
							"});"
						]
					}
				}
			],
			"request": {
				"auth": {
					"type": "bearer",
					"bearer": [
						{
							"key": "token",
							"value": "{{iamtoken}}",
							"type": "string"
						}
					]
				},
				"method": "DELETE",
				"header": [
					{
						"key": "ibm-service-instance-id",
						"value": "{{serviceid}}"
					}
				],
				"body": {
					"mode": "raw",
					"raw": ""
				},
				"url": {
					"raw": "https://{{endpoint-region}}cloud-object-storage.appdomain.cloud/{{bucket}}",
					"protocol": "https",
					"host": [
						"{{endpoint-region}}",
						"objectstorage",
						"softlayer",
						"net"
					],
					"path": [
						"{{bucket}}"
					]
				},
				"description": "Create new bucket"
			},
			"response": []
		},
		{
			"name": "Create new bucket (different storage class)",
			"event": [
				{
					"listen": "test",
					"script": {
						"id": "5cf3d531-13f1-4cf6-a4fc-2f8d2d8e48af",
						"type": "text/javascript",
						"exec": [
							"pm.test(\"Request was successful\", function() {",
							"   pm.response.to.be.success; ",
							"});"
						]
					}
				}
			],
			"request": {
				"auth": {
					"type": "bearer",
					"bearer": [
						{
							"key": "token",
							"value": "{{iamtoken}}",
							"type": "string"
						}
					]
				},
				"method": "PUT",
				"header": [
					{
						"key": "ibm-service-instance-id",
						"value": "{{serviceid}}"
					},
					{
						"key": "Content-Type",
						"value": "application/x-www-form-urlencoded"
					}
				],
				"body": {
					"mode": "raw",
					"raw": "<CreateBucketConfiguration>\n\t<LocationConstraint>{{bucketlocationvault}}</LocationConstraint>\n</CreateBucketConfiguration>"
				},
				"url": {
					"raw": "https://{{endpoint-region}}cloud-object-storage.appdomain.cloud/{{bucket}}vault",
					"protocol": "https",
					"host": [
						"{{endpoint-region}}",
						"objectstorage",
						"softlayer",
						"net"
					],
					"path": [
						"{{bucket}}vault"
					]
				}
			},
			"response": []
		},
		{
			"name": "Delete bucket (different storage class)",
			"event": [
				{
					"listen": "test",
					"script": {
						"id": "5cf3d531-13f1-4cf6-a4fc-2f8d2d8e48af",
						"type": "text/javascript",
						"exec": [
							"pm.test(\"Request was successful\", function() {",
							"   pm.response.to.be.success; ",
							"});"
						]
					}
				}
			],
			"request": {
				"auth": {
					"type": "bearer",
					"bearer": [
						{
							"key": "token",
							"value": "{{iamtoken}}",
							"type": "string"
						}
					]
				},
				"method": "DELETE",
				"header": [
					{
						"key": "ibm-service-instance-id",
						"value": "{{serviceid}}"
					}
				],
				"body": {
					"mode": "raw",
					"raw": ""
				},
				"url": {
					"raw": "https://{{endpoint-region}}cloud-object-storage.appdomain.cloud/{{bucket}}vault",
					"protocol": "https",
					"host": [
						"{{endpoint-region}}",
						"objectstorage",
						"softlayer",
						"net"
					],
					"path": [
						"{{bucket}}vault"
					]
				}
			},
			"response": []
		},
		{
			"name": "Create new bucket (key protect)",
			"event": [
				{
					"listen": "test",
					"script": {
						"id": "5cf3d531-13f1-4cf6-a4fc-2f8d2d8e48af",
						"type": "text/javascript",
						"exec": [
							"pm.test(\"Request was successful\", function() {",
							"   pm.response.to.be.success; ",
							"});"
						]
					}
				}
			],
			"request": {
				"auth": {
					"type": "bearer",
					"bearer": [
						{
							"key": "token",
							"value": "{{iamtoken}}",
							"type": "string"
						}
					]
				},
				"method": "PUT",
				"header": [
					{
						"key": "ibm-service-instance-id",
						"value": "{{serviceid}}"
					},
					{
						"key": "ibm-sse-kp-encryption-algorithm",
						"value": "AES256"
					},
					{
						"key": "ibm-sse-kp-customer-root-key-crn",
						"value": "{{rootkeycrn}}"
					}
				],
				"body": {
					"mode": "raw",
					"raw": ""
				},
				"url": {
					"raw": "https://{{endpoint-region}}cloud-object-storage.appdomain.cloud/{{bucket}}kp",
					"protocol": "https",
					"host": [
						"{{endpoint-region}}",
						"objectstorage",
						"softlayer",
						"net"
					],
					"path": [
						"{{bucket}}kp"
					]
				}
			},
			"response": []
		},
		{
			"name": "Delete bucket (key protect)",
			"event": [
				{
					"listen": "test",
					"script": {
						"id": "5cf3d531-13f1-4cf6-a4fc-2f8d2d8e48af",
						"type": "text/javascript",
						"exec": [
							"pm.test(\"Request was successful\", function() {",
							"   pm.response.to.be.success; ",
							"});"
						]
					}
				}
			],
			"request": {
				"auth": {
					"type": "bearer",
					"bearer": [
						{
							"key": "token",
							"value": "{{iamtoken}}",
							"type": "string"
						}
					]
				},
				"method": "DELETE",
				"header": [
					{
						"key": "ibm-service-instance-id",
						"value": "{{serviceid}}"
					}
				],
				"body": {
					"mode": "raw",
					"raw": ""
				},
				"url": {
					"raw": "https://{{endpoint-region}}cloud-object-storage.appdomain.cloud/{{bucket}}kp",
					"protocol": "https",
					"host": [
						"{{endpoint-region}}",
						"objectstorage",
						"softlayer",
						"net"
					],
					"path": [
						"{{bucket}}kp"
					]
				}
			},
			"response": []
		}
	],
	"event": [
		{
			"listen": "prerequest",
			"script": {
				"id": "08caf505-3991-4273-8027-db00d867680f",
				"type": "text/javascript",
				"exec": [
					""
				]
			}
		},
		{
			"listen": "test",
			"script": {
				"id": "6e61fa13-e3b9-42f0-8c38-9d3468748922",
				"type": "text/javascript",
				"exec": [
					""
				]
			}
		}
	],
	"variable": [
		{
			"id": "643d5480-e629-4e26-b244-1b3c3a85195a",
			"key": "bucket",
			"value": "jsaitocosbucketapitest41",
			"type": "string",
			"description": ""
		},
		{
			"id": "7554e40a-f4d5-4938-972e-43b28ce52ad1",
			"key": "serviceid",
			"value": "crn:v1:bluemix:public:cloud-object-storage:global:a/1d524cd94a0dda86fd8eff3191340732:8888b05b-a143-4917-9d8e-9d5b326a1604::",
			"type": "string",
			"description": ""
		},
		{
			"id": "f06bc8b2-3476-47a6-aacb-c603dd808310",
			"key": "iamtoken",
			"value": "eyJraWQiOiIyMDE3MTAzMC0wMDowMDowMCIsImFsZyI6IlJTMjU2In0.eyJpYW1faWQiOiJJQk1pZC01MFkxNjdNOFRZIiwiaWQiOiJJQk1pZC01MFkxNjdNOFRZIiwicmVhbG1pZCI6IklCTWlkIiwiaWRlbnRpZmllciI6IjUwWTE2N004VFkiLCJnaXZlbl9uYW1lIjoiSmFtZXMiLCJmYW1pbHlfbmFtZSI6IlNhaXRvIiwibmFtZSI6IkphbWVzIFNhaXRvIiwiZW1haWwiOiJKYW1lcy5TYWl0bzFAaWJtLmNvbSIsInN1YiI6IkphbWVzLlNhaXRvMUBpYm0uY29tIiwiYWNjb3VudCI6eyJic3MiOiIxZDUyNGNkOTRhMGRkYTg2ZmQ4ZWZmMzE5MTM0MDczMiJ9LCJpYXQiOjE1Mjg2MDk0ODIsImV4cCI6MTUyODYxMzA4MiwiaXNzIjoiaHR0cHM6Ly9pYW0uYmx1ZW1peC5uZXQvaWRlbnRpdHkiLCJncmFudF90eXBlIjoidXJuOmlibTpwYXJhbXM6b2F1dGg6Z3JhbnQtdHlwZTphcGlrZXkiLCJzY29wZSI6ImlibSBvcGVuaWQiLCJjbGllbnRfaWQiOiJieCIsImFjciI6MSwiYW1yIjpbInB3ZCJdfQ.iG5ey13QlXHu3OH81v8J5sduKv3NzSqCFng_DlpNglPo07K-nWst3O9o6iEJfm0AzJwYNTZqWVru1pjI-KcAPXFBe503DPIf6cYolAw4rarU5booW-pdzk8-R5HZ7MJK7b8sxJtm7PAilXVZvl5yFE-tJsFkeMH6XCIj_R9i6dwSemDBL3Juq79_x3KsgJGFg37p5f2vck1_7gR7nSb03m8m3mCvrrx7zGkLDuM8NXAVlwwxKcitwlG_UfEBwSbX3krF04zF2tFCpGkcWAnuaFdaVTOL6uaULxCi5BWHqzLXIPhAoyfnVGNv0TBJtyFcpJ1LdlsBmz7RS2fROlDf2w",
			"type": "string",
			"description": ""
		},
		{
			"id": "dba24a46-e1df-4201-9a9b-f247fff22315",
			"key": "endpoint-region",
			"value": "s3.us-south",
			"type": "string",
			"description": ""
		},
		{
			"id": "ca751949-eea0-4f36-9f58-aea1ed324cd5",
			"key": "rootkeycrn",
			"value": "crn:v1:bluemix:public:kms:us-south:a/1d524cd94a0dda86fd8eff3191340732:90b7a1db-0fe2-4de9-b90e-922c127ff530:key:0b43e36e-a863-40e2-b713-5caa2bf99288",
			"type": "string",
			"description": ""
		},
		{
			"id": "e486732e-c9bc-4ec4-8bf9-04b66d513e5a",
			"key": "bucketlocationvault",
			"value": "us-south-vault",
			"type": "string",
			"description": ""
		}
	]
}
```

### Import the collection to Postman
{: #postman-import-collection}

1. In Postman click **Import** in the upper right corner
2. Import the Collection file by using either of these methods:
    * From the Import window drag the Collection file into the window labeled **Drop files here**
    * Click the Choose Files button and browse to the folder and select the Collection file
3. *IBM COS* now appears in the Collections window
4. Expand the Collection and see 20 sample requests
5. Click the three dots to the right of the collection to expand the menu and click **Edit**
6. Edit the variables to match your Cloud Storage environment
    * `bucket` - Enter the name for the new bucket you want to create (bucket names must be unique across Cloud Storage).
    * `serviceid` - Enter the CRN of your Cloud Storage service. Instructions to obtain your CRN are available [here](/docs/account?topic=account-crn#service-instance-crn).
    * `iamtoken` - Enter the OAUTH token for your Cloud Storage service. Instructions to obtain your OAUTH token are available [here](/docs/services/key-protect?topic=key-protect-retrieve-access-token).
    * `endpoint` - Enter the regional endpoint for your Cloud Storage service. Obtain the available endpoints from the [IBM Cloud Dashboard](https://cloud.ibm.com/resources/){: external}
        * *Ensure that your selected endpoint matches your key protect service to ensure that the samples run correctly*
    * `rootkeycrn` - The CRN of the Root Key created in your primary Key Protect service.
        * The CRN resembles `crn:v1:bluemix:public:kms:us-south:a/3d624cd74a0dea86ed8efe3101341742:90b6a1db-0fe1-4fe9-b91e-962c327df531:key:0bg3e33e-a866-50f2-b715-5cba2bc93234`
        * *Ensure the Key Protect service that is selected matches the region of the Endpoint*
    * `bucketlocationvault` - Enter the location constraint value for the bucket creation for the *Create New Bucket (different storage class)* API request.
        * Acceptable values include:
            * `us-south-vault`
            * `us-standard-flex`
            * `eu-cold`
7. Click Update

### Running the samples
{: #postman-samples}
The API sample requests are fairly straightforward and easy to use. They're designed to run in order and demonstrate how to interact with Cloud Storage. You can also run a functional test against your Cloud Storage service to ensure proper operation.

| Request                                                    | Expected Result                                                                                                                                                                               | Test Results                                                                                       |
|------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Retrieve list of buckets                                   | In the Body, you set an XML list of the buckets in your cloud storage.                                                                                                                        | Request was successful. Response contains expected content                                         |
| Create new bucket                                          | Status Code 200 OK                                                                                                                                                                            | Request was successful                                                                             |
| Create new text file                                       | Status Code 200 OK                                                                                                                                                                            | Request was successful. Response contains expected header                                          |
| Create new binary file                                     | Click **Body** and click **Choose File** to select an image to upload.                                                                                                                                | Request was successful. Response contains expected header                                          |
| Retrieve list of files from bucket                         | In the Body of the response you see the two files you created in the previous requests.                                                                                                | Request was successful. Response contains expected header                                          |
| Retrieve list of files from bucket (filter by prefix)      | Change the querystring value to `prefix=<some text>`. In the body of the response you see the files with names that start with the prefix specified.                                   | Request was successful. Response contains expected header                                          |
| Retrieve text file                                         | In the Body of the response you see the text you entered in the previous request                                                                                                       | Request was successful. Response contains expected body content. Response contains expected header |
| Retrieve binary file                                       | In the Body of the response you see the image you chose in the previous request.                                                                                                       | Request was successful. Response contains expected header                                          |
| Retrieve list of failed multipart uploads                  | In the Body of the response you see any failed multipart uploads for the bucket.                                                                                                       | Request was successful. Response contains expected content                                         |
| Retrieve list of failed multipart uploads (filter by name) | Change the querystring value to `prefix=<some text>`. In the body of the response you see any failed multipart uploads for the bucket with names that start with the prefix specified. | Request was successful. Response contains expected content                                         |
| Set CORS enabled bucket                                    | Status Code 200 OK                                                                                                                                                                            | Request was successful                                                                             |
| Retrieve bucket CORS config                                | In the body of the response you see the CORS configuration set for the bucket                                                                                                         | Request was successful. Response contains expected content                                         |
| Delete bucket CORS config                                  | Status Code 200 OK                                                                                                                                                                            | Request was successful                                                                             |
| Delete text file                                           | Status Code 200 OK                                                                                                                                                                            | Request was successful                                                                             |
| Delete binary file                                         | Status Code 200 OK                                                                                                                                                                            | Request was successful                                                                             |
| Delete bucket                                              | Status Code 200 OK                                                                                                                                                                            | Request was successful                                                                             |
| Create new bucket (different storage class)                | Status Code 200 OK                                                                                                                                                                            | Request was successful                                                                             |
| Delete bucket (different storage class)                    | Status Code 200 OK                                                                                                                                                                            | Request was successful                                                                             |
| Create new bucket (key protect)                            | Status Code 200 OK                                                                                                                                                                            | Request was successful                                                                             |
| Delete bucket (key protect)                                | Status Code 200 OK                                                                                                                                                                            | Request was successful                                                                             |


## Using the Postman Collection Runner
{: #postman-runner}

The Postman Collection Runner provides a user interface for testing a collection and allows you to run all requests in a Collection at once. 

1. Click the Runner button in the upper right corner on the main Postman window.
2. In the Runner window, select the IBM COS collection and click the big blue **run IBM COS** button at the bottom of the screen.
3. The Collection Runner window will show the iterations as the requests are run. You will see that the test results appear below each of the requests.
    * The **Run Summary** displays a grid view of the requests and allows filtering of the results.
    * You can also click **Export Results** to save the results to a JSON file.
