---

copyright:
  years: 2017, 2020
lastupdated: "2020-04-29"

keywords: gui, desktop, cyberduck

subcollection: cloud-object-storage


---

{{site.data.keyword.attribute-definition-list}}

# Transferring files with Cyberduck
{: #cyberduck}

Cyberduck is a popular, open source, and easy-to-use graphical interface for {{site.data.keyword.cos_full}}.
{: shortdesc}

Cyberduck provides full operational visibility in connecting to {{site.data.keyword.cos_full_notm}}. Cyberduck is downloaded from [cyberduck.io/](https://cyberduck.io/){: external}.

Using Cyberduck to create a connection to {{site.data.keyword.cos_short}} and synchronize a folder of local files to a bucket, follow these steps:

1. Download, install, and start Cyberduck.
1. The application opens and you can create a connection to {{site.data.keyword.cos_short}}. Click **Open Connection** to configure the connection.
1. A pop-up window opens. From the menu, select the option, `Amazon S3`. Enter your information into the following fields:

   * `Server`: enter the appropriate endpoint for your data at {{site.data.keyword.cos_full_notm}}

   Ensure that the endpoint region matches the intended bucket. For more information about endpoints, see [Endpoints and storage locations](/docs/cloud-object-storage?topic=cloud-object-storage-endpoints#endpoints).
   {: important}

   * `Access Key ID` generated by selecting the appropriate HMAC option when creating a [Service Credential](/docs/cloud-object-storage/iam?topic=cloud-object-storage-service-credentials);
   * `Secret Access Key` also from the HMAC option.
   * `Add to Keychain`: Save the configuration to the your personal keychain *(optional)*.
   * Ignore the other options, like `Anonymous Login` checkbox, and `SSH Private Key`.

1. Cyberduck takes you to the root of the account where buckets can be created.
   * Right-click within the main pane and select **New Folder** (*the application deals with many transfer protocols where Folder is the more common name for a container construct*).
   * Enter the bucket name and then click Create.
1. After the bucket is created, double-click the bucket to view it. Within the bucket you can perform various functions such as:
   * Upload files to the bucket
   * List bucket contents
   * Download objects from the bucket
   * Synchronize local files to a bucket
   * Synchronize objects to another bucket
   * Create an archive of a bucket
1. Right-click within the bucket and select **Synchronize**. A pop-up window opens where you can browse to the folder that you want to synchronize to the bucket. Select the folder and click Choose.
1. After you select the folder, a new pop-up window opens. Here, a drop-down menu is available where you select the synchronization operation with the bucket. Three possible synchronize options are available from the menu:

   * `Download`: This downloads changed and missing objects from the bucket.
   * `Upload`: This uploads changed and missing files to the bucket.
   * `Mirror`: This performs both download and upload operations, ensuring that all new and updated files and objects are synchronized between the local folder and the bucket.

 8. Another window opens to show active and historical transfer requests. After the synchronization request is complete, the main window will perform a list operation on the bucket to show the updated content in the bucket.

## Mountain Duck
{: #mountain-duck}

Mountain Duck builds upon Cyberduck to allow you to mount cloud Object Storage as a disk in Finder on Mac or Explorer on Windows. Trial versions are available but a registration key is required for continued use.

Creating a bookmark in Mountain Duck is similar to creating connections in Cyberduck:

1. Download, install, and start Mountain Duck
2. Create a New Bookmark
3. From the drop-down menu select `Amazon S3` and enter the following information:
   * `Server`: enter the appropriate endpoint where the bucket has been configured.
     
   Ensure that the endpoint region matches the intended bucket. For more information about endpoints, see [Endpoints and storage locations](/docs/cloud-object-storage?topic=cloud-object-storage-endpoints#endpoints).{: important}
   
   * `Username`: enter the Access Key
   * Click **Connect**
   * You're prompted for your Secret Key, which will then be saved in the keychain.

Your buckets will now be available in Finder or Explorer. You can interact with {{site.data.keyword.cos_short}} like any other mounted file system.

## CLI
{: #cyberduck-cli}

Cyberduck also provides `duck`, a command-line interface (CLI) that runs in shell on Linux, Mac OS X, and Windows. Installation instructions are available on the `duck` [wiki page](https://trac.cyberduck.io/wiki/help/en/howto/cli#Installation){: external}.

In order to use `duck` with {{site.data.keyword.cos_full}}, a custom profile needs to be added to the [Application Support Directory](https://trac.cyberduck.io/wiki/help/en/howto/cli#Profiles){: external}. Detailed information about `duck` connection profiles is available on the [CLI help](https://trac.cyberduck.io/wiki/help/en/howto/profiles){: external}.

Here is an example profile for a regional COS endpoint:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
    <dict>
        <key>Protocol</key>
        <string>s3</string>
        <key>Vendor</key>
        <string>cos</string>
        <key>Scheme</key>
        <string>https</string>
	    <key>Default Hostname</key>
	    <string>s3.us-south.objectstorage.s3.us-south.cloud-object-storage.appdomain.cloud.net</string>
        <key>Description</key>
        <string>IBM COS</string>
        <key>Default Port</key>
        <string>443</string>
        <key>Hostname Configurable</key>
        <true/>
        <key>Port Configurable</key>
        <true/>
        <key>Username Configurable</key>
        <true/>
    </dict>
</plist>
```
{: codeblock}

For more information about endpoints, see [Endpoints and storage locations](/docs/cloud-object-storage?topic=cloud-object-storage-endpoints#endpoints).

Adding this profile to `duck` allows you to access {{site.data.keyword.cos_short}} using a command similar to below:

```bash
duck --nokeychain --longlist cos://<bucket-name> --username <access-key> --password <secret-access-key>
```
{: pre}

*Key Values*
* `<bucket-name>` - name of the COS bucket (*ensure that bucket and endpoint regions are consistent*)
* `<access-key>` - HMAC access key
* `<secret-access-key>` - HMAC secret key

```
Login successful…
---	May 31, 2018 1:48:16 AM		mynewfile1.txt
---	May 31, 2018 1:49:26 AM		mynewfile12.txt
---	Aug 10, 2018 9:49:08 AM		newbigfile.pdf
---	May 29, 2018 3:36:50 PM		newkptestfile.txt
```
{: screen}

A full list of command-line options is available by entering `duck --help` in the shell is available in the [wiki site](https://trac.cyberduck.io/wiki/help/en/howto/cli#Usage){: external}.