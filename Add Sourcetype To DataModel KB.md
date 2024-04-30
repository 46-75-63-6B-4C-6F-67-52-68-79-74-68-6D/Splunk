# Mapping Sourcetype To Data Model

Mapping data to Splunk data models helps streamline data analysis, improve search performance, and enhance the effectiveness of Splunk deployments for various use cases, including IT operations, security, compliance, and business analytics.

<br />

## General Steps
* Discovery
* Create an Eventtype for the sourcetype or specific subset of data
* Create the corresponding Tag
* Modify the CIM Macro
* Validate

<br />*I will use an example for 'index=palo_alto sourcetype=pan:threat'*

<br />

### Discovery 
Under Settings > Data Models > Data Model Name - *(In this example, I will be using the 'Web; Data Model)*<br />
* Take note of the Dataset(s) - In this case we see 'Proxy' & 'Storage'
* Take note of the Constraint - This is where you can find the CIM macro name which is 'cim_Web_indexes' and then also the tag name which we will use later which is 'web'.

![Alt text](https://github.com/46-75-63-6B-4C-6F-67-52-68-79-74-68-6D/Splunk/blob/main/Resources/web_datamodel.jpeg "Splunk Web Data Model")


<br />

### Create the Eventtype
Under Settings > Event types > New Event Type<br />
<br />Define the below settings:
* Name - Unique Name, for example "web_palo_alto"
* Search String - Define your sourcetype / search string (SPL)
* Tag(s) - Define the Data Model Tag here
* Make sure the app is set to "Search"
* Make sure 'Permissions' are set to 'Global'
<br />*In the below example, we are using the "web" data model which has two data sets 'web' & 'proxy' thats why two tags are being used.*

![Alt text](https://github.com/46-75-63-6B-4C-6F-67-52-68-79-74-68-6D/Splunk/blob/main/Resources/datamodel_eventtype2.jpeg "Splunk Event Type")


<br />

### Create the corresponding Tag
Under Settings > Tags > All unique tag objects > New Tag<br />
<br />Define the below settings:
* Tag Name - Define your Tag here
* Field Value Pair - Define the Eventtype we created above
* Make sure the app is set to "Search"
* Make sure 'Permissions' are set to 'Global'
<br />*In the below example, we are using the "web" data model which has two data sets 'web' & 'proxy' thats why we see two separate entries for each tag.*

![Alt text](https://github.com/46-75-63-6B-4C-6F-67-52-68-79-74-68-6D/Splunk/blob/main/Resources/datamodel_tag.jpeg "Splunk Tag")


<br />

### Modify the CIM Macro
Under Settings > Advanced Search > Search Macros > Filter on the macro name we located in the "Discovery" steps (cim_Web_indexes)<br />
* Name - Do not modify this, this is the macro used by the specific data model.
* Definition - Define index/sourcetypes in SPL<br />
<br />*In the below example you will see multiple indexes defined, this is just to show what you might expect to see.*

![Alt text](https://github.com/46-75-63-6B-4C-6F-67-52-68-79-74-68-6D/Splunk/blob/main/Resources/search_macro.jpeg "CIM Search Macro")


<br />

### Validate
Run the below search to return a list of sourcetypes mapped to the data model - *We should see the newly added sourcetype after a few minutes*
````
| datamodel Web search | stats count by sourcetype
````

<br />You can also run the below variation
````
| datamodel Web search | stats values(sourcetype)
````

<br />
Run the below search to validate fields names are parsing/translating correctly within the data model. In the screenshot below you can see the field names are properly translating to the following format 'datamodel_name.field_name' For example, the 'action' field should return as 'Web.action' from this search. We also want to make sure the field values are returning the correct values and not return as "unknown" (Below shows the values as Allowed & Blocked). You will want to ensure the new sourcetype is parsing the required datamodel fields which can be found Under Settings > Data Models > Data Model Name > Scroll down and you will see a list of fields to cross check with this search.
<br /><br />

*In the below search I am using the 'Web' datamodel and the 'pan:threat' sourcetype. In this example, we can see the field names are translating with the data model name and are not returning as "unknown"*

````
| datamodel Web search | search sourcetype=pan:threat
````

![Alt text](https://github.com/Splunk_Admin/blob/main/Resources/datamodel_field.jpeg "Data Model Field")

<br />

Below is an example search you can modify per your data model field settings (Under Settings > Data Models > Data Model Name). I used AI to assist as this can potenitally take some time if your data model has a lot of extracted fields its looking for. Copy the list of field names within your data model and have AI replace the SPL to accommadate your expected field names and data model name. In my example I using the "Web" data model and searching specifically on the "pan:threat" sourcetype. If you wanted to use a different data model you would append 'Malware' to the beginning of each field name. for example, "values(Malware.user) as user" and so on.<br />
This will return all the field values per field on the 'pan:threat' sourcetype. Look through and make sure parsing is correct and we are not seeing a bunch of "unknown" values or values that dont seem to belong to the field name, for example, a url value under the 'src_ip' field.
````
| datamodel Web search 
| stats values(Web.user) as user, 
        values(Web.action) as action, 
        values(Web.app) as app, 
        values(Web.category) as category, 
        values(Web.dest_ip) as dest_ip, 
        values(Web.dest_port) as dest_port, 
        values(Web.dns_root_domain) as dns_root_domain, 
        values(Web.dns_tld) as dns_tld, 
        values(Web.domain) as domain, 
        values(Web.http_content_type) as http_content_type, 
        values(Web.http_method) as http_method, 
        values(Web.http_referrer) as http_referrer, 
        values(Web.http_user_agent) as http_user_agent, 
        values(Web.response_code) as response_code, 
        values(Web.rule) as rule, 
        values(Web.severity) as severity, 
        values(Web.severity_id) as severity_id, 
        values(Web.signature) as signature, 
        values(Web.signature_id) as signature_id, 
        values(Web.src_ip) as src_ip, 
        values(Web.src_port) as src_port, 
        values(Web.status) as status, 
        values(Web.sub_type) as sub_type, 
        values(Web.tag) as tag, 
        values(Web.uri_path) as uri_path, 
        values(Web.uri_query) as uri_query, 
        values(Web.url) as url
  by sourcetype 
| search sourcetype="pan:threat"
````

<br />*Make any parsing changes to the specific fields if you find anything parsing incorrectly.*
