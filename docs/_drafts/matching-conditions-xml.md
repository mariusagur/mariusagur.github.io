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