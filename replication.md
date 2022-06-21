---

copyright:
  years: 2022
lastupdated: "2022-06-15"

keywords: data, replication, loss prevention

subcollection: cloud-object-storage


---

{{site.data.keyword.attribute-definition-list}}

# Replicating objects
{: #replication-overview}

Replication allows users to define rules for automatic, asynchronous copying of objects from source bucket to one or many destination buckets in the same or different locations. 
{: shortdesc}

## What is replication?
{: #replication-what}

Replication copies newly created objects and object updates from a source bucket to one or more destination buckets.

- Only new objects or new versions of the existing objects (created after the replication rule) are copied to the destination bucket. Existing objects can be replicated by 
- The metadata of the source object is applied to the replicated object.
- Bi-directional replication rules between two buckets requires rules to be active on both buckets.
- Filters (prefix, tags) can be used to scope the replication rule to only apply to a subset of objects in a source bucket.

## Why use replication?
{: #replication-why}

- Keep copies of data across buckets in multiple geographic locations, or even multiple Cross Region buckets.
- Meet compliance regulations for data sovereignty by defining replication rules that store replicas only within the allowable locations.
- Keep production and test data in sync, as replication retains object metadata such as last modified time, version ID, etc.
- Manage the storage class and lifecycle policies for the replicated objects independent of the source, by defining a different storage class and/or lifecycle rules for the destination bucket. Similarly, you can store replicas in a bucket in a separate service instance or even IBM Cloud account, and also independently control access to the replicas.

## Getting started with replication
{: #replication-gs}

In order to get started, there are some some prerequisites: 

- You'll need the `Writer` or `Manager` platform role on the source bucket, or a custom role with the appropriate replication actions (such as  `cloud-object-storage.bucket.put_replication`) assigned.
- You don't need to actually be able to access the destination bucket, but do need to have sufficient platform roles to create new IAM policies that allow the source bucket to write to the destination bucket.
- Both the source and destination buckets must have versioning enabled.
- The target bucket must not have a legacy bucket firewall enabled. 
- Objects encrypted using SSE-C cannot be replicated, although managed encryption (SSE-KMS) like Key Protect is fully compatible with replication.
- Objects in an archived state cannot be replicated.

As versioning is a requirement for replication, it is not possible to replicate objects in buckets configured with an Immutable Object Storage policy.
{: note}

1. After navigating to your chosen source bucket, click the **Configuration** tab.
2. Look for **Bucket replication** and click the **Setup replication** button.
3. Select **Replication source** and click **Next**.
4. Assuming the destination bucket is in the same IBM Cloud account, select the instance and bucket from the drop-down menus.  Alternatively, toggle the radio button to **No** and paste in the CRN of the destination bucket.
5. Click on the **Check permissions** button.

Now, you'll need to grant the source bucket `Writer` permissions on the destination bucket. There are several ways to do this, but the easiest is to use the IBM Cloud Shell and the IBM Cloud CLI.

1. Open an IBM Cloud Shell in a new window or tab.
2. Copy the IBM Cloud CLI command shown in the Object storage console, and paste it into the new shell.
3. Return to the bucket configuration window or tab, and click on the **Check permissions** button again.

Now you'll create a replication rule.

1. Ensure the rule status radio button is set to **Enabled**.
2. Give the rule a name and a priority, as well as any prefix or tag filters that will limit the objects subject to the replication rule.
3. Click **Done**.

## Terminology
{: #replication-terminology}

**Source bucket**: The bucket for which a replication policy is configured. It is the source of replicated objects.

**Target bucket**: The bucket that is defined as the destination in the source bucket replication policy. It is the target of replicated objects. Also referred to as a 'destination' bucket.

**Replica**: The new object created in a target bucket as a result of a request made to a source bucket. 


## Consistency and data integrity
{: #replication-consistency}

While IBM Cloud Object Storage provides strong consistency for all data IO operations, bucket configuration is eventually consistent. After enabling replication rules for the first time on a bucket, it may take a few moments for the configuration to propagate across the system and new objects to start being replicated. 

## IAM actions
{: #replication-iam}

There are new IAM actions associated with replication. 

| IAM Action                                     | Role                    |
|------------------------------------------------|-------------------------|
| `cloud-object-storage.bucket.get_replication`    | Manager, Writer, Reader |
| `cloud-object-storage.bucket.put_replication`    | Manager, Writer         |
| `cloud-object-storage.bucket.delete_replication` | Manager, Writer         |

## Activity Tracker events 
{: #replication-at}

Replication generates additional events.

- `cloud-object-storage.bucket-replication.create`
- `cloud-object-storage.bucket-replication.read`
- `cloud-object-storage.bucket-replication.delete`
- `cloud-object-storage.object-replication.sync` (generated at the source)
- `cloud-object-storage.object-replication.create` (generated at the destination)


For `cloud-object-storage.bucket-replication.create` events, the following fields provide extra information:

| Field                                             | Description                                                                  |
|---------------------------------------------------|------------------------------------------------------------------------------|
| `requestData.replication.num_sync_remote_buckets` | The number of destination buckets specified in the bucket replication rules. |
| `requestData.replication.failed_remote_sync`      | The CRNs of the buckets that failed the replication check.                   |

When replication is active, operations on objects may generate the following extra information:

| Field                                           | Description                                                                                                                                                                                                                                                                                            |
|-------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `requestData.replication.replication_throttled` | Indicates if the replication of the object was delayed on the source due to a throttling mechanism.                                                                                                                                                                                                    |
| `requestData.replication.destination_bucket_id` | The CRN of the destination bucket.                                                                                                                                                                                                                                                                     |
| `requestData.replication.sync_type`             | The type of sync operation. A `content` sync indicates that the object data _and_ any metadata was written to the destination, a `metadata` sync indicates that only metadata was written to the destination, and a `delete` sync indicates that only delete markers were written to the destination.. |
| `responseData.replication.source_bucket_id`     | The CRN of the source bucket.                                                                                                                                                                                                                                                                          |
| `responseData.replication.result`               | Values can be `success`, `failure` (indicates a server error), `user` (indicates a user error).                                                                                                                                                                                                        |
| `responseData.replication.message`              | The HTTP response message (such as `OK`).                                                                                                                                                                                                                                                              |

You can trace an object from when it is written to the source until it is written on the target. Search for the request ID associated with the object write and three events should appear: 

1. The original `PUT`,
2. The sync request from the source,
3. The PUT request on the target.  
   
Any of these three missing indicates a failure. 

## Usage and accounting
{: #replication-usage}

All replicas are objects themselves, and contribute usage just like any other data. Successful replication results in billable `PUT`, `GET`, and `HEAD` requests, although any bandwidth consumed in the replication process is not billed.  

Replication generates additional metrics for use with IBM Cloud Monitoring:

- `ibm_cos_bucket_replication_sync_requests_issued`
- `ibm_cos_bucket_replication_sync_requests_received`

## Interactions
{: #replication-interactions}

### Versioning
{: #replication-interactions-versioning}

Versioning is mandatory in order to enable replication. After you enable versioning on both the source and destination buckets and configure replication on the source bucket, you may encounter the following issues:

- If you attempt to disable versioning on the source bucket, {{site.data.keyword.cos_short}} returns an error. You must remove the replication configuration before you can disable versioning on the source bucket.
- If you disable versioning on the destination bucket, replication fails. 

### Key Protect encryption
{: #replication-interactions-kp}

Source objects will be encrypted using the root key of the source bucket, and replicas are encrypted using the root key of the destination bucket.

### Lifecycle configurations
{: #replication-interactions-lifecycle}

If a lifecycle policy is enabled on a destination bucket, the lifecycle actions will be based on the original creation time of the object at the source, not the time that the replica becomes available in the destination bucket. 

### Immutable Object Storage
{: #replication-interactions-worm}

Using retention policies is not possible on a bucket with versioning enabled, and as versioning is a requirement for replication, it is not possible to replicate objects to or from a bucket with Immutable Object Storage enabled.

### Legacy bucket firewalls
{: #replication-interactions-firewall}

Buckets using legacy firewalls to restrict access based on IP addresses are not able to use replication, as the background services that replicate the objects do not have fixed IP addresses and can not pass the firewall.  

It is recommended to instead use context-based restrictions for controlling access based on network information.  

## Cloud Functions and Code Engine
{: #replication-interactions-functions}

Replication does not provide a trigger for Cloud Functions or Code Engine events at this time.

## Replicating existing objects
{: #replication-existing}

A replication rule can only act on objects that are written _after_ the rule is configured and applied to a bucket.  If there are existing objects in a bucket that should be replicated, the replication processes needs to be made aware of the existence of the objects. This can be easily accomplished by using the `PUT copy` operation to copy objects onto themselves. 

This process will reset some object metadata, including creation timestamps.  This will impact lifecycle policies and any other services that use creation or modification timestamps (such as content delivery networks).  Ensure that any disruptions that may arise from resetting object metadata are dealt with appropriately.
{: important}

The process involves:

1. Creating a list of all the objects in a bucket that should be subject to replication rules,
2. Iterating over that list, performing a `PUT copy` operation on each object with the source being identical to the target of the request.

This example will only replicate the new version of the object created by the `PUT copy` request.  In order to replicate all versions of the object, it would be necessary to copy each individual version as well.
{: note}

The following example is written in Python, but the algorithm could be applied in any programming language or context.

```py
import os
import sys
import ibm_boto3
from ibm_botocore.config import Config

# Create client connection
cos = ibm_boto3.client("s3",
                       ibm_api_key_id=os.environ.get('IBMCLOUD_API_KEY'),
                       ibm_service_instance_id=os.environ['SERVICE_INSTANCE_ID'],
                       config=Config(signature_version="oauth"),
                       endpoint_url=os.environ['US_GEO']
                       )

# Define the bucket with existing objects for replication
bucket = os.environ['BUCKET']

def copy_in_place(BUCKET_NAME):
    print("Priming existing objects in " + bucket + " for replication...")

    paginator = cos.get_paginator('list_objects_v2')
    pages = paginator.paginate(Bucket=bucket)

    for page in pages:
        for obj in page['Contents']:
            key = obj['Key']
            print("  * Copying " + key + " in place...")
            try:            
                headers = cos.head_object(
                    Bucket=bucket,
                    Key=key
                    )
                
                md = headers["Metadata"]
                
                cos.copy_object(
                    CopySource={
                        'Bucket': bucket,
                        'Key': key
                        },
                    Bucket=bucket,
                    Key=key,
                    TaggingDirective='COPY'
                    MetadataDirective='REPLACE',
                    Metadata=md
                    )
                print("    Success!")
            except Exception as e:
                print("    Unable to copy object: {0}".format(e))
    print("Existing objects in " + bucket + " are now subject to replication rules.")

copy_in_place(bucket)
```

## REST API examples
{: #replication-apis-examples}

The following examples are shown using cURL for ease of use. Environment variables are used to represent user specific elements such as `$BUCKET`, `$TOKEN`, and `$REGION`.  Note that `$REGION` would also include any network type specifications, so sending a request to a bucket in `us-south` using the private network would require setting the variable to `private.us-south`.

### Enable replication on a bucket
{: #replication-apis-enable}

The replication configuration is provided as XML in the body of the request.  New requests will overwrite any existing replication rules that are present on the bucket.

A replication configuration must include at least one rule, and can contain a maximum of 1,000. Each rule identifies a subset of objects to replicate by filtering the objects in the source bucket. To choose additional subsets of objects to replicate, add a rule for each subset.

To specify a subset of the objects in the source bucket to apply a replication rule to, add the `Filter` element as a child of the `Rule` element. You can filter objects based on an object key prefix, one or more object tags, or both. When you add the `Filter` element in the configuration, you must also add the following elements: `DeleteMarkerReplication`, `Status`, and `Priority`.

Header                    | Type   | Description
--------------------------|--------|----------------------------------------------------------------------------------------------------------------------
`Content-MD5` | String | **Required**: The base64 encoded 128-bit MD5 hash of the payload, which is used as an integrity check to ensure that the payload wasn't altered in transit.

The body of the request must contain an XML block with the following schema:

| Element                    | Type      | Children                                                                       | Ancestor                   | Constraint                                                                                                                                                                                                                                                                                                                                                                                        |
|----------------------------|-----------|--------------------------------------------------------------------------------|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ReplicationConfiguration` | Container | `Rule`                                                                         | None                       | Limit 1.                                                                                                                                                                                                                                                                                                                                                                                          |
| `Rule`                     | Container | `ID`, `Status`, `Filter`, `DeleteMarkerReplication`, `Destination`, `Priority` | `ReplicationConfiguration` | Limit 1000.                                                                                                                                                                                                                                                                                                                                                                                       |
| `ID`                       | String    | None                                                                           | `Rule`                     | Must consist of (`a-z,`A-Z0-9`) and the following symbols: `!` `_` `.` `*` `'` `(` `)` `-`                                                                                                                                                                                                                                                                                                        |
| `Destination`              | Container | `Bucket`                                                                       | `Rule`                     | Limit 1.                                                                                                                                                                                                                                                                                                                                                                                          |
| `Bucket`                   | String    | None                                                                           | `Destination`              | The CRN of the destination bucket.                                                                                                                                                                                                                                                                                                                                                                |
| `Priority`                 | Integer   | None                                                                           | `Rule`                     | The priority indicates which rule has precedence whenever two or more replication rules conflict. Object storage will attempt to replicate objects according to all replication rules. However, if there are two or more rules with the same destination bucket, then objects will be replicated according to the rule with the highest priority. The higher the number, the higher the priority. |
| `Status`                   | String    | None                                                                           | `Rule`                     | Specifies whether the rule is enabled. Valid values are `Enabled` or `Disabled`.                                                                                                                                                                                                                                                                                                                  |
| `DeleteMarkerReplication`  | Container | `Status`                                                                       | `Rule`                     | Limit 1.                                                                                                                                                                                                                                                                                                                                                                                          |
| `Status`                   | String    | None                                                                           | `DeleteMarkerReplication`   | Specifies whether Object storage replicates delete markers.  Valid values are `Enabled` or `Disabled`.                                                                                                                                                                                                                                                                                            |
| `Filter`                   | String    | `Prefix`                                                                       | `Rule`                     | A filter that identifies the subset of objects to which the replication rule applies. A `Filter` must specify exactly one `Prefix`, `Tag`, or an `And` child element.                                                                                                                                                                                                                             |
| `Prefix`                   | String    | None                                                                           | `Filter`                   | An object key name prefix that identifies the subset of objects to which the rule applies.                                                                                                                                                                                                                                                                                                        |
| `Tag`                      | String    | None                                                                           | `Filter`                   | A container for specifying a tag key and value. The rule applies only to objects that have the tag in their tag set.                                                                                                                                                                                                                                                                              |
| `And`                      | String    | None                                                                           | `Filter`                   | A container for specifying rule filters. The filters determine the subset of objects to which the rule applies. This element is required only if you specify more than one filter.                                                                                                                                                                                                                |
| `Key`                      | String    | None                                                                           | `Tag`                      | The tag key.                                                                                                                                                                                                                                                                                                                                                                                      |
| `Value`                    | String    | None                                                                           | `Tag`                      | The tag value.                                                                                                                                                                                                                                                                                                                                                                                    |


This example will replicate any new objects, but will not replicate delete markers.  

```
curl -X "PUT" "https://$BUCKET.s3.$REGION.cloud-object-storage.appdomain.cloud/?replication" \
     -H 'Authorization: bearer $TOKEN' \
     -H 'Content-MD5: exuBoz2kFBykNwqu64JZuA==' \
     -H 'Content-Type: text/plain; charset=utf-8' \
     -d $'<ReplicationConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
            <Rule>
              <ID>SimpleReplication</ID>
              <Priority>1</Priority>
              <Status>Enabled</Status>
              <DeleteMarkerReplication>
                <Status>Disabled</Status>
              </DeleteMarkerReplication>
              <Filter/>
              <Destination>
                <Bucket>$DESTINATION_CRN</Bucket>
              </Destination>
          	</Rule>
          </ReplicationConfiguration>'
```

A successful request returns a `200` response.


### View replication configuration for a bucket
{: #replication-apis-read}

```
curl -X "GET" "https://$BUCKET.s3.$REGION.cloud-object-storage.appdomain.cloud/?replication" \
     -H 'Authorization: bearer $TOKEN' 
```

This returns an XML response body with the appropriate schema:

```xml
<ReplicationConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
  <Rule>
    <ID>SimpleReplication</ID>
    <Status>ENABLED</Status>
    <DeleteMarkerReplication>
      <Status>DISABLED</Status>
    </DeleteMarkerReplication>
    <Destination>
      <Bucket>crn:v1:bluemix:public:cloud-object-storage:global:a/9978e07eXXXXXXXX66c89c428028654:ef1c725e-XXXX-4967-bcc1-734c03a2b846:bucket:replication-destination</Bucket>
    </Destination>
    <Priority>1</Priority>
    <Filter/>
  </Rule>
</ReplicationConfiguration>
```

### Delete the replication configuration for a bucket
{: #replication-apis-delete}

```
curl -X "DELETE" "https://$BUCKET.s3.$REGION.cloud-object-storage.appdomain.cloud/?replication" \
     -H 'Authorization: bearer $TOKEN' 
```

A successful request returns a `204` response.

## SDK examples
{: #replication-sdks}

The following examples make use of the IBM COS SDKs for Python and Node.js, although the implementation of object versioning should be fully compatible with any S3-compatible library or tool that allows for the setting of custom endpoints.  Using third-party tools requires HMAC credentials in order to calculate AWS V4 signatures.  For more information on HMAC credentials, [see the documentation](https://cloud.ibm.com/docs/cloud-object-storage?topic=cloud-object-storage-uhc-hmac-credentials-main).  

### Python
{: #versioning-sdks-python}

Enabling versioning using the IBM COS SDK for Python can be done using the [low-level client](https://ibm.github.io/ibm-cos-sdk-python/reference/services/s3.html#client) syntax.

Using a client:

```python
#!/usr/bin/env python3

import ibm_boto3
from ibm_botocore.config import Config
from ibm_botocore.exceptions import ClientError

# Define constants
API_KEY = os.environ.get('IBMCLOUD_API_KEY')
SERVICE_INSTANCE = os.environ.get('SERVICE_INSTANCE_ID')
ENDPOINT = os.environ.get('ENDPOINT')

BUCKET = "my-replication-bucket" # The bucket that will enable replication.

# Create resource client with configuration info pulled from environment variables.
cosClient = ibm_boto3.client("s3",
                         ibm_api_key_id=API_KEY,
                         ibm_service_instance_id=SERVICE_INSTANCE,
                         config=Config(signature_version="oauth"),
                         endpoint_url=ENDPOINT
                         )

response = cosClient.put_bucket_versioning(
    Bucket=BUCKET,
    ReplicationConfiguration={
        'Rules': [
            {
                'ID': 'string',
                'Priority': 123,
                'Filter': {
                    'Prefix': 'string',
                    'Tag': {
                        'Key': 'string',
                        'Value': 'string'
                    },
                    'And': {
                        'Prefix': 'string',
                        'Tags': [
                            {
                                'Key': 'string',
                                'Value': 'string'
                            },
                        ]
                    }
                },
                'Status': 'Enabled'|'Disabled',
                'Destination': {
                    'Bucket': 'string',
                },
                'DeleteMarkerReplication': {
                    'Status': 'Enabled'|'Disabled'
                }
            },
        ]
    }
)
```

Listing the versions of an object using the same client: 

```python
resp = cosClient.list_object_versions(Prefix='some-prefix', Bucket=BUCKET)
```

Note that the Python APIs are very flexible, and there are many different ways to accomplish the same task.  

### Node.js
{: #versioning-sdks-node}

Enabling versioning using the [IBM COS SDK for Node.js](https://ibm.github.io/ibm-cos-sdk-js/AWS/S3.html#putBucketVersioning-property):

```js
const IBM = require('ibm-cos-sdk');

var config = {
    endpoint: '<endpoint>',
    apiKeyId: '<api-key>',
    serviceInstanceId: '<resource-instance-id>',
};

var cos = new IBM.S3(config);

var params = {
  Bucket: 'STRING_VALUE', /* required */
  ReplicationConfiguration: { /* required */
    Role: 'STRING_VALUE', /* required */
    Rules: [ /* required */
      {
        Destination: { /* required */
          Bucket: 'STRING_VALUE', /* required */
        },
        Status: Enabled | Disabled, /* required */
        Filter: {
          And: {
            Prefix: 'STRING_VALUE',
            Tags: [
              {
                Key: 'STRING_VALUE', /* required */
                Value: 'STRING_VALUE' /* required */
              },
              /* more items */
            ]
          },
          Prefix: 'STRING_VALUE',
          Tag: {
            Key: 'STRING_VALUE', /* required */
            Value: 'STRING_VALUE' /* required */
          }
        },
        ID: 'STRING_VALUE',
        Prefix: 'STRING_VALUE',
        Priority: 'NUMBER_VALUE',
        }
      }
    ]
  },
  ContentMD5: 'STRING_VALUE',
};
cos.putBucketReplication(params, function(err, data) {
  if (err) console.log(err, err.stack); // an error occurred
  else     console.log(data);           // successful response
});
```