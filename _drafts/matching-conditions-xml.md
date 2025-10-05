---
layout: post
title:  "Record identification in Contact Center with Copilot Studio"
date:   2025-04-05 18:26:56 +0200
categories: contact center,copilot studio,omnichannel
---

# Use record identification rules for customize record matching in Contact Center

A somewhat [hidden feature](https://learn.microsoft.com/en-us/dynamics365/contact-center/extend/enable-fields-identify-customers) in the Dynamics omnichannel is the record identification rule. Now I say hidden, but it is documented and used around the world so it's just hard to find if you don't know what you're looking for.
A record identification rule is simply a fetchXml specified in a dedicated column for the workstream record in Dataverse  
![screenshot of the workstream table with record identification rule column](/assets/img/workstream_table_identificationrule.png)  
If you extract the column value from the Record Identification Rule you can see the default rule used to match an incoming conversation. (I say default, but there is an actual default rule in the background which will match on authenticated column data, like msdyn_fromphone, [more here](https://learn.microsoft.com/en-us/dynamics365/customer-service/administer/context-variables-for-bot) )
<details>
  <summary>Default FetchXML for voice workstreams</summary>
{% highlight XML %}
<RecordIdentificationRuleSet>
    <RecordIdentificationRule>
        <PrimaryEntity LogicalCollectionName="accounts" PrimaryKeyAttribute="accountid"
            PrimaryNameAttribute="name" />
        <fetch version="1.0" output-format="xml-platform" mapping="logical" top="2">
            <entity name="account">
                <attribute name="accountid" />
                <attribute name="name" />
                <filter type="and">
                    <condition attribute="statuscode" operator="eq" value="1" />
                    <condition attribute="name" operator="eq" value="${Name}" />
                    <condition attribute="telephone1" operator="eq"
                        source="msdyn_msdyn_ocliveworkitem_msdyn_ocphonecallengagementctx_liveworkitemid"
                        value="${msdyn_fromphone}" />
                    <condition attribute="emailaddress1" operator="eq" value="${Email}" />
                </filter>
            </entity>
        </fetch>
        <ContextKey name="msdyn_account_msdyn_ocliveworkitem_Customer" isPreferred="false" />
    </RecordIdentificationRule>
    <RecordIdentificationRule>
        <PrimaryEntity LogicalCollectionName="contacts" PrimaryKeyAttribute="contactid"
            PrimaryNameAttribute="fullname" />
        <fetch version="1.0" output-format="xml-platform" mapping="logical" top="2">
            <entity name="contact">
                <attribute name="contactid" />
                <attribute name="fullname" />
                <filter type="and">
                    <condition attribute="statuscode" operator="eq" value="1" />
                    <condition attribute="fullname" operator="eq" value="${Name}" />
                    <condition attribute="mobilephone" operator="eq"
                        source="msdyn_msdyn_ocliveworkitem_msdyn_ocphonecallengagementctx_liveworkitemid"
                        value="${msdyn_fromphone}" />
                    <condition attribute="emailaddress1" operator="eq" value="${Email}" />
                </filter>
            </entity>
        </fetch>
        <ContextKey name="msdyn_contact_msdyn_ocliveworkitem_Customer" isPreferred="true" />
    </RecordIdentificationRule>
    <RecordIdentificationRule>
        <PrimaryEntity LogicalCollectionName="incidents" PrimaryKeyAttribute="incidentid"
            PrimaryNameAttribute="title" />
        <fetch version="1.0" output-format="xml-platform" mapping="logical" top="2">
            <entity name="incident">
                <attribute name="incidentid" />
                <attribute name="title" />
                <filter type="and">
                    <condition attribute="ticketnumber" operator="eq" value="${CaseNumber}" />
                    <condition attribute="statuscode" operator="eq" value="1" />
                    <filter type="or">
                        <filter type="and">
                            <condition attribute="statuscode" operator="eq" value="1"
                                entityname="ac" />
                            <condition attribute="name" operator="eq" value="${Name}"
                                entityname="ac" />
                            <condition attribute="telephone1" operator="eq"
                                source="msdyn_msdyn_ocliveworkitem_msdyn_ocphonecallengagementctx_liveworkitemid"
                                value="${msdyn_fromphone}" entityname="ac" />
                            <condition attribute="emailaddress1" operator="eq" value="${Email}"
                                entityname="ac" />
                        </filter>
                        <filter type="and">
                            <condition attribute="statuscode" operator="eq" value="1"
                                entityname="co" />
                            <condition attribute="fullname" operator="eq" value="${Name}"
                                entityname="co" />
                            <condition attribute="mobilephone" operator="eq"
                                source="msdyn_msdyn_ocliveworkitem_msdyn_ocphonecallengagementctx_liveworkitemid"
                                value="${msdyn_fromphone}" entityname="co" />
                            <condition attribute="emailaddress1" operator="eq" value="${Email}"
                                entityname="co" />
                        </filter>
                    </filter>
                </filter>
                <link-entity name="account" from="accountid" to="customerid" link-type="outer"
                    alias="ac" />
                <link-entity name="contact" from="contactid" to="customerid" link-type="outer"
                    alias="co" />
            </entity>
        </fetch>
        <ContextKey name="msdyn_incident_msdyn_ocliveworkitem" />
    </RecordIdentificationRule>
</RecordIdentificationRuleSet>
{% endhighlight %}
</details>