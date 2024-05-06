theme: jekyll-theme-minimal

# anvil-core

The anvil-core Java library is intended to be a secure and stable way to interact with the Kyvos REST API, Kyvos Java API, and the Azure Databricks REST API.  This is currently done via the `AnvilClient` class and the individual `AzureRestApiClient`,`KyvosRestApiClient`, and `KyvosJavaApiClient` client classes.

For convenience, a Runner class is included in the library for testing basic connectivity to the various services.
<p>
Note: The Kyvos Java API client currently must be run either from a VM in Azure or possibly via a VPN connection as the
servers it connects to are not exposed to the Internet.
</p>

```
usage: com.merative.anvil.Runner
 -acid,--azureClientId <arg>            The azure client id
 -acs,--azureClientSecret <arg>         The azure client secret
 -atid,--azure-tenant-id <arg>          The azure tenant id
 -avo,--anvil-views-overrides           Execute Anvil SQL views overrides
 -bcube,--build-cube                    Invoke processing of the semantic
                                        model
 -cac,--create-cluster                  Create Azure Databricks Cluster
                                        for a given tenant
 -ccube,--create-cube                   Create the semantic model using
                                        the metadata in the metadata
                                        directory
 -cdrd,--create-drd                     Create DRD using the metadata in
                                        the metadata directory
 -cf,--create-folders                   Create tenant folders
 -cg,--create-groups                    Create tenant permission groups
 -ckc,--create-kyvos-connection         Create kyvos connection for a
                                        databricks cluster
 -crf,--create-rf                       Create datasets using the metadata
                                        in the metadata directory
 -cs,--create-schema                    Create Databricks schema
 -dac,--delete-cluster                  Delete Azure Databricks Cluster
                                        for a given tenant
 -dkc,--delete-kyvos-connection         Delete kyvos connection for a
                                        databricks cluster
 -exp,--export-cube                     Export semantic model metadata
 -h,--help                              Print this message
 -kv,--key-vault-name <arg>             The name of the Key Vault
 -msbid,--metadata-svc-batch-id <arg>   The metadata service batch id
 -oavo,--ops-anvil-views-overrides      Execute Ops and Anvil SQL views
                                        overrides
 -ovo,--ops-views-overrides             Execute Ops SQL views overrides
 -p,--properties-file <arg>             Configuration properties file
 -pd,--publish-dashboards               Publish dashboards in tenant
                                        folder and link dashbords to tenant cube
 -sac,--start-azure-cluster             Start Azure Databricks Cluster
 -tid,--tenant-id <arg>                 The tenant id
 -uac,--update-cluster                  Update a selected Azure Databricks
                                        Cluster
 -ucube,--update-cube                   Update the semantic model using
                                        the metadata in the metadata
                                        directory
 -udrd,--update-drd                     Update the DRD using the metadata
                                        in the metadata directory
 -urf,--update-rf                       Update datasets using the metadata
                                        in the metadata directory
 -wcl,--workbook-cube-linker            Workbook -> semantic-model linker
```

Within the usage described above there are several required arguments in addition to the task you want to run:
 - the Key Vault name (-kv).
 - the customer's tenant id (-tid).
 - the Azure tenant id (-atid) where the Azure components are deployed.
 - the Azure client id (-acid) used to authenticate to perform Azure operations such as access Key Vault.
 - the Azure client secret (-acs).
 - the Metadata Service batch id (-msbid).

Example:
```
-kv hibi-kv -tid 0000 -atid aaaa -acid bbbb -acs cccc -msbid my-batch-id
```

# Setting up your environment to run the Anvil automation

In order for automation to run it requires that you have access to a Key Vault instance containing all the properties (as secrets) required for the automation to work.

Your Key Vault instance should contain these properties:

### Kyvos properties:
```
# Kyvos common configuration
kyvos-user-id=<username>
kyvos-user-password=<token>

<tenant-id>-kyvos-connection-name=DefaultConnectionName
<tenant-id>-databricks-database-name=hi_kyvos_db

# Kyvos REST API configuration
kyvos-host-url=<hostname>
kyvos-port=8443

k8s-mount-path=</anvil/>
k8s-config-path=</anvil/config/>
k8s-standard-metadata-path=</anvil/metadata/anvil-content/>
k8s-dashboard-metadata-path=</anvil/metadata/kyvos-dashboards/>
k8s-tenant-metadata-path=</anvil/<tenant-id>/metadata/>

# Kyvos Java API configurationkyvos-java-api-configuration.properties
kyvos.properties.file=<k8s-config-path>/kyvosclient.properties
kyvos.global.properties.file=<k8s-config-path>/global.properties

# Optional Kyvos workbook->semantic-model linker properties
<tenant-id>-kyvos-workbook-folder-name=<my-custom-wb-folder>
<tenant-id>-kyvos-workbook-name=<my-custom-wb>
```
Notes
+ `kyvos-user-id` is the username that you log on to Kyvos UI with, shown on the Kyvos UI's User Menu (top RH corner)
+ `kyvos-user-password` is the value of the token you generated in the Kyvos UI: User Menu > Security > Personal Access Token > Generate Token
+ `<tenant-id>-databricks-database-name` is the name of an existing Kyvos database: Toolbox (hamburger menu on Kyvos UI's LH side) > Semantic Models
+ See section [Usage Scenarios](#usage-scenarios) for instructions about using `<tenant-id>-kyvos-workbook-folder-name` and `<tenant-id>-kyvos-workbook-name`.

The Kyvos Java API will require:
+ `kyvosclient.properties` to be found at \<k8s-config-path\>/kyvosclient.properties
+ `global.properties` to be found at \<k8s-config-path\>/global.properties

The Anvil automation will require:
+ `metadata files` to be found at \<k8s-standard-metadata-path\>/
+ `custom metadata` to be found at \<k8s-tenant-metadata-path\>/
+ `dashboard metadata` to be found at \<k8s-dashboard-metadata-path\>/

### Azure Databricks key vault properties:
```
# Azure Databricks REST API configuration
<tenant-id>-sp-client-id=<ID of an Azure AD application>
<tenant-id>-sp-client-secret=<the application's client secrets>
<tenant-id>-sp-display-name=<The Azure Databricks service principal display name. This value varies from environments>
<tenant-id>-databricks-http-path=<Tenant Databricks cluster http path>
<tenant-id>sp-databricks-token=<The Databricks token, which is created automatically as a secret in your provided key vault instance when the -cac (CreateOrUpdateCluster) has run>
<tenant-id>-k8s-ops-sql-override-path=<Path to the directory containing the tenant-specific viewsOverride.sql>
master-sp-client-id=<The master azure client id>
master-sp-client-secret=<The master azure client secrets>
databricks-host-name=<hostname of the Azure Databricks instance>
azure-tenant-id=<ID of the application's Azure AD tenant>
databricks-secrets-scope=<The Azure Databricks scope to be used to create secrets>
k8s-anvil-sql-override-path=<Path to the directory containing the tenant-wide viewsOverride.sql>

```
### Properties For Metadata Service GetViewSchema Integration
The anvil-core code uses a tenant's Views Schema information for several of its functions, such as designing a semantic model. This Views Schema information can be sourced either from a call to the Claims Pipeline's (CP) Metadata Service's getViewSchema CodeQL API endpoint, or from static JSON files in anvil-content, such as getViewSchema.json.

At a minimum, these properties/secrets need to be set in your Key Vault instance:
```
metadata-svc-key-vault-name=<The name of the metadata service's Key Vault instance>
metadata-svc-endpoint=<The GraphQL API endpoint for the metadata service>
is-metadata-svc-gvs=<The boolean value that determines if the code invokes CP metadata-svc's getViewSchema API>
```
Notes
- If `is-metadata-svc-gvs` is set to _true_, the anvil-core code uses the response from metadata-svc's getViewSchema API, otherwise it uses anvil-content's getViewSchema.json.
- The metadata service's Key Vault instance contains the AZURE_CLIENT_ID and AZURE_CLIENT_SECRET secrets that the anvil-core code will use to call the Metadata Service's GraphQL API endpoints. During a run of the anvil-core JAR, developers can eschew Key Vault in favour of using a properties file using the CLI option `-p <path_to_properties_file>`; if that's the case then make sure you add these properties to your properties file:
```
metadata-svc-key-vault-name=<The name of the metadata service's Key Vault instance>
metadata-svc-endpoint=<The GraphQL API endpoint for the metadata service>
metadata-svc-batch-id=<The batch ID for the metadata service>
is-metadata-svc-gvs=<The boolean value that determines if the code invokes CP metadata-svc's getViewSchema API>
```
#### Optional Metadata Service Tenant/Customer ID
In order to successfully interact with the metadata service's getViewSchema endpoint, a specific tenant/customer ID is used in conjunction with the batch ID in the call. In production, this tenant/customer ID will be the same as the Anvil tenant ID. However, a developer might be using an Anvil tenant ID that doesn't match the tenant/customer ID associated with the getViewSchema call. In that case, an optional property called `metadata-svc-tenant-id` can be added to the properties (see below). If the property is present and set to a value, the code will use that value as the tenant/customer ID in the call to the getViewSchema endpoint.
```
metadata-svc-tenant-id=<The optional tenant/customer ID used to interact with the metadata service>
```

### Java Truststore File
Get the `truststore.jks` file from a colleague, save it locally, then update `global.properties`:
```
TRUSTSTORE_PATH=<path to local JKS files>/truststore.jks
```
## For Development Use
Developers can eschew Key Vault in favour of using a properties file during a run of the JAR by using the CLI option `-p <path_to_properties_file>`.
### Workbook -> Semantic-Model Linker Task (Custom Names for Workbook and Workbook Folder)
The `-wcl` workbook -> semantic-model linker Runner task is used to link a one or more workbooks to a tenant's semantic model. How the values of the following two properties are set will determine the behaviour of the linker task.
```
kyvos-workbook-folder-name=<my_custom_workbook_folder_name>
kyvos-workbook-name=<my_custom_workbook_name>
```
#### Usage Scenarios
1. If neither of those properties' values is set, then the `-wcl` task uses the default value for the workbook folder name, which is currently `<tenant_id> Curated Content`, and links _all_ workbooks in that folder to the tenant's semantic model.
2. If both of those properties' values are set then the `-wcl` task uses those values for the workbook folder name and workbook name, and links that workbook in the folder to the tenant's semantic model.
3. If the `kyvos-workbook-folder-name` property value is set, and the `kyvos-workbook-name` value is not set, then the task will link _all_ workbooks contained in that folder to the tenant's semantic model.
4. If the `kyvos-workbook-folder-name` property value is not set, and the `kyvos-workbook-name` property value is set, then the `-wcl` task will look in the default folder (i.e. `<tenant_id> Curated Content`) and link the workbook whose name is the `kyvos-workbook-folder-name` property value to the tenant's semantic model.

#### Usage Scenarios: Summary
| kyvos-workbook-folder-name | kyvos-workbook-name | Result                                      |
| :---: | :---: |:--------------------------------------------|
| 0 | 0 | Link all wbs in default folder              |
| 1 | 1 | Link custom named wb in custom named folder |
| 1 | 0 | Link all wbs in custom named folder         |
| 0 | 1 | Link custom named wb in default folder      |

## Running the Anvil automation client
Once the above step has been done, invoke the [Runner](https://github.com/hibiscus-anvil/anvil-core/blob/main/src/main/java/com/merative/anvil/Runner.java) class, passing the required CLI args.

### Logging properties
The overall logging level of anvil-core defaults to DEBUG and the default logging level of Apache Zookeeper messages
is INFO.  Both can be controlled by Java system properties on the command line.

For setting the overall logging level for anvil-core, the **anvil-log-level** property can be used, for example to set the
level to INFO rather than the default of DEBUG, you could pass the following arguments to the anvil-core command line
command:
```
-Danvil-log-level=INFO
```
For setting the logging level for Apache Zookeeper log messages, you can use the **anvil-zk-log-level** property.  For
example, to change the Zookeeper logging level to DEBUG from its default of INFO, you could pass the following arguments
to the anvil-core command line command:
```
-Danvil-zk-log-level=DEBUG
```
## Building
We use Gradle 7.6 for building the library; all non-gradle plugin dependencies are pulled from the CommOps Artifactory.

Building this code now requires that the `artifactoryUser` and `artifactoryPassword` variables are set so that Gradle can access Artifactory.

You can create an Artifactory API key or identity token to use as your password here:
https://artifactory.commops.truvenhealth.com/ui/admin/artifactory/user_profile
Then create a file ~/.gradle/gradle.properties and set it up like this:

```
artifactoryUser=YOUR_USERNAME@merative.com
artifactoryPassword=API KEY or IDENTITY TOKEN
```
#### Cluster spark configuration

Add spark_config.json to create a cluster. To update cluster spark configuration, alter this file and run -uac

Path to add the files: `/src/main/resources`

**Info:** _client.secret, client.id, msi.tenant are Azure Databricks client service principal_
**Info:** _client.endpoint is the URL of the Azure Databricks instance associated with Application ID_
**Info:** _client.secret, client.id are retrieved via key vault

spark_config.json contains an initial spark configuration to be used to create a cluster and should be as follows:

```json
{
  "spark.sql.parquet.int96AsTimestamp": "true",
  "spark.databricks.delta.preview.enabled": "true",
  "spark.hadoop.spark.sql.parquet.binaryAsString": "false",
  "spark.hadoop.fs.azure.account.oauth2.client.secret": "",
  "spark.databricks.preemption.enabled": "false",
  "spark.hadoop.fs.azure.account.oauth2.client.endpoint": "",
  "spark.sql.parquet.binaryAsString": "false",
  "spark.databricks.service.server.enabled": "true",
  "spark.hadoop.fs.azure.account.oauth2.client.id": "",
  "spark.sql.caseSensitive": "false",
  "spark.hadoop.fs.azure.account.auth.type": "OAuth",
  "spark.hadoop.fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
  "spark.hadoop.spark.sql.parquet.int96AsTimestamp": "true",
  "spark.hadoop.spark.sql.caseSensitive": "false",
  "spark.hadoop.fs.azure.account.oauth2.msi.tenant": ""
}
```
