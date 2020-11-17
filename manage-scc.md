---
copyright:
  years: 2020
lastupdated: "2020-11-12
"

keywords: security and compliance for cloud-object-storage, security for cloud-object-storage, compliance for cloud-object-storage

subcollection: cloud-object-storage
---

{:external: target="_blank" .external}
{:note: .note}
{:term: .term}
{:shortdesc: .shortdesc}
{:table: .aria-labeledby="caption"}


# Managing security and compliance with {{site.data.keyword.cos_full_notm}}
{: #manage-security-compliance}

{{site.data.keyword.cos_full_notm}} is integrated with the [{{site.data.keyword.compliance_short}}](/docs/security-compliance) to help you manage security and compliance for your organization.
{: shortdesc}

With the {{site.data.keyword.compliance_short}}, you can:

* Monitor for controls and goals that pertain to {{site.data.keyword.cos_short}}.
* Define rules for {{site.data.keyword.cos_short}} that can help to standardize resource configuration.

This service only supports the ability to view the results of your configuration scans in the Security and Compliance Center.
{:note}

## Monitoring security and compliance posture with {{site.data.keyword.cos_short}}
{: #monitor-cloud-object-storage}

As a security or compliance focal, you can use the {{site.data.keyword.cos_short}} [goals](x2117978){: term} to help ensure that your organization is adhering to the external and internal standards for your industry. By using the {{site.data.keyword.compliance_short}} to validate the resource configurations in your account against a [profile](x2034950){: term}, you can identity potential issues as they arise.

All of the goals for {{site.data.keyword.cos_short}} are added to the {{site.data.keyword.cloud_notm}} Best Practices Controls 1.0 profile but can also be mapped to other profiles.
{: note}

To start monitoring your resources, check out [Getting started with {{site.data.keyword.compliance_short}}](/docs/security-compliance?topic-security-compliance-getting-started)

### Available goals for {{site.data.keyword.cos_short}}
{: #cloud-object-storage-available-goals}

* Ensure that Cloud Object Storage buckets are only created in designated locations
* Ensure that Cloud Object Storage buckets are only created in a certain storage class
* Ensure that Cloud Object Storage buckets are created with a designated managed encyption root key CRN
* Ensure that Cloud Object Storage buckets are prevented from setting public ACLs
* Ensure that Cloud Object Storage buckets are accessible by using private or direct endpoints only
* Ensure that Cloud Object Storage buckets are only accessible from designated IP addresses
* Ensure that Cloud Object Storage buckets can not be accessed from designated IP addresses
* Ensure that Cloud Object Storage buckets use a designated instance of Activity Tracker
* Ensure that Cloud Object Storage buckets send object read and/or write events to Activity Tracker

## Governing {{site.data.keyword.cos_short}} resource configuration
{: #govern-cloud-object-storage}

As a security or compliance focal, you can use the {{site.data.keyword.compliance_short}} to define configuration rules for the instances of {{site.data.keyword.cos_short}} that you create.

[Configuration rules](x3084914){: term} are used to enforce the configuration standards that you want to implement across your accounts. To learn more about the data that you can use to create a rule for {{site.data.keyword.cos_short}}, review the following table. For more information about configuration rules, see [What makes up a rule](/docs/security-compliance?topic=security-compliance-what-is-rule).

| Resource kind | Property                                               | Operator type | Description                                                                                                                                                                                                      |
|---------------|--------------------------------------------------------|---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| *bucket*      | *location*                                             | String        | Bucket location. This is configured during bucket creation in the request endpoint.                                                                                                                              |
| *bucket*      | *storage_class*                                        | String        | Bucket storage class. This is configured during bucket creation in the `LocationConstraint` field.                                                                                                               |
| *bucket*      | *ibm_sse_kms_customer_root_key_crn*                    | String        | Bucket SSE Key Protect or Hyper Protect Crypto Services Customer Root Key CRN. This maps to the bucket configuration parameter, `ibm-sse-kp-customer-root-key-crn`.                                              |
| *bucket*      | *public_access_block_configuration.block_public_acls*  | String        | Setting to prevent future configuration of ACLs that permit public access on the bucket and its objects. Prior public access configuration for the bucket and its objects is unchanged.                          |
| *bucket*      | *public_access_block_configuration.ignore_public_acls* | String        | Setting to ignore configuration of public ACLs on the bucket and its objects, rendering effective access as private. GET Bucket ACL and GET Object ACL return effective (enforced) permissions for the resource. |
| *bucket*      | *firewall.allowed_network_type*                        | String        | List of network endpoint types that are allowed. Refer to COS Resource Configuration API for the list of valid values.                                                                                           |
| *bucket*      | *firewall.allowed_ip*                                  | String        | List of allowed originating IP addresses/ranges. The list can contain up to 1000 IPv4 or IPv6 addresses/ranges in CIDR notation.                                                                                 |
| *bucket*      | *firewall.denied_ip*                                   | String        | List of originating IP addresses/ranges that are not permitted. The list can contain up to 1000 IPv4 or IPv6 addresses/ranges in CIDR notation.                                                                  |
| *bucket*      | *activity_tracking.activity_tracker_crn*               | String        | CRN of the Activity Tracker instance that receives management events and based on opt-in configuration, read and write data events.                                                                              |
| *bucket*      | *activity_tracking.write_data_events*                  | String        | Opt-in to send the bucket's object write data events (i.e. uploads) to the configured Activity Tracker instance.                                                                                                 |
| *bucket*      | *activity_tracking.read_data_events*                   | String        | Opt-in to send the bucket's object read events (i.e. downloads) to the configured Activity Tracker instance.                                                                                                     |

{: caption="Table 1. Rule properties for {{site.data.keyword.cos_short}}" caption-side="top"}

To learn more about config rules, check out [What is a config rule?](/docs/security-compliance?topic=security-compliance-what-is-rule).