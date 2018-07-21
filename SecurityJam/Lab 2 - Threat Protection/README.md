# Security : Threat Protection

*Duration : 15 mins*

*Persona : API Team / Security*

# Use case

You have an existing Apigee API proxy that takes requests from the Internet and forwards them to an existing service. You have a requirement to ensure the integrity of the API message content, by protecting against threats such as JSON/XML/SQL injection and other mallicious manipulation. 

# How can Apigee Edge help?

Message content is a significant attack vector used by malicious API consumers. Apigee Edge provides a set of out-of-the-box policies that help mitigate the potential for your backend services to be compromised by attackers or by malformed request payloads.

In this lab we will see how to use the following policies:
 - JSON Threat Protection policy
 - XML Threat Protection policy

# Pre-requisites

* Basic understanding of [JSON](https://www.json.org/) and [XML](https://www.w3.org/TR/2008/REC-xml-20081126) data formats.
* Completed a previous [Virtual API Jam](https://github.com/rmistry75/devjam3/tree/master/Labs/VirtualAPIJam) or have the equivalent knowledge of API lifecycle management, and specifically, API proxy policy configuration and enforcement on Apigee Edge.
* Completed [Lab 1 - Traffic Management](https://github.com/aliceinapiland/AdvancedVirtualAPIJam/tree/master/SecurityJam/Lab%201%20Traffic%20Management%20-%20Throttle%20APIs), of this Virtual API Jam on Security

# Instructions

## JSON Threat Protection

1. In your org, select **Develop → API Proxies** in the side navigation menu, and select the API proxy previously created in Lab 1.

![image alt text](./media/image_5.png)

2. Click on the **Develop** tab to access the API Proxy development dashboard.

![image alt text](./media/image_9.png)

3. Click on the "**Send request and view request headers and body**" flow under Proxy Endpoints default, and then click on **+Step** on the upper right of the Request flow to attach a JSON Threat Pretection policy.

![image alt text](./media/select-json-flow-for-policy.png)

4. Select **JSON Threat Protection** policy. Click on **Add** button to add the policy to the selected flow's request pipeline.

![image alt text](./media/add-policy.png)

5. Select the policy to display the policy's XML configuration in the editor.

![image alt text](./media/select-policy-config.png)

6. Change the policy's XML configuration to the below snippet to enforce protection against JSON payload manipulation threats.
```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<JSONThreatProtection async="false" continueOnError="false" enabled="true" name="JSON-Threat-Protection-1">
    <DisplayName>JSON Threat Protection-1</DisplayName>
    <Properties/>
    <ObjectEntryCount>5</ObjectEntryCount>
    <Source>request</Source>
</JSONThreatProtection>
```

In the above example, we use the JSON Threat Protection policy to ensure that the incoming API request JSON payload does not contain more than 5 fields. If the incoming payload contains more than 5 fields, the API proxy returns an error response.
For a full list of JSON integrity checks that can be performed using this policy, see the [JSON Threat Protection policy documentation](https://docs.apigee.com/api-platform/reference/policies/json-threat-protection-policy#elementreference).

7. Click on **Save** to save the API Proxy changes.

![image alt text](./media/save-changes.png)

### To Test JSON Threat Protection:

1. To test the changes made, first click on **Trace** tab of the API proxy dashboard, and click on **Start Trace Session** button.

![image alt text](./media/start-tracev2.png)

* **Note**: Take note of the Apigee `organization` and `environment` you are working in. In the screenshot above, the organization is `amer-demo16` and the environment is `test`. Your organization name will likely end in `*-eval`
2. Now, send a POST request to your API endpoint at **http://{{your-organization}}-{{your-environment}}.apigee.net/mock-target-api/echo** with the following format:
```
POST /mock-target-api/echo HTTP/1.1
Host: {{your org}}-{{your env}}.apigee.net
Content-Type: application/json

{
"field1": "test_value1",
"field2": "test_value2",
"field3": "test_value3",
"field4": "test_value4",
"field5": "test_value5",
"field6": "test_value6"
}
```

You can make this call either using a REST client like the one [here](https://apigee-rest-client.appspot.com/), or using a terminal command such as **curl**.
```
curl -X POST "http://{{your-org}}-{{your-env}}.apigee.net/mock-target-api/echo" -H "Content-Type: application/json" -d '{"field1": "test_value1", "field2": "test_value2", "field3": "test_value3", "field4": "test_value4", "field5": "test_value5", "field6": "test_value6"}'
```
* **Note:** If you are using a REST client, make sure that your HTTP request has a Header name/value pair of `Content-Type: application/json` as shown below

![image alt text](./media/add-json-header.png)

3. The response received will be an error, since we attempted to send more than 5 fields in the POST request payload.

![image alt text](./media/error-response.png)

We also see that the JSON Threat Protection policy was triggered to return this error response, when we see the Trace screen.

![image alt text](./media/error-response-trace.png)

4. You can now test for a successful API call, by sending the API endpoint a similar POST request, but this time with 5 or fewer fields in the JSON payload.
```
POST /mock-target-api/echo HTTP/1.1
Host: {{your-org}}-{{your-env}}.apigee.net
Content-Type: application/json

{
"field1": "test_value1",
"field2": "test_value2",
"field3": "test_value3",
"field4": "test_value4",
"field5": "test_value5"
}
```

You can make this call either using a REST client like the one [here](https://apigee-rest-client.appspot.com/), or using a terminal command such as **curl**.
```
curl -X POST "http://{{your-org}}-{{your-env}}.apigee.net/mock-target-api/echo" -H "Content-Type: application/json" -d '{"field1": "test_value1", "field2": "test_value2", "field3": "test_value3", "field4": "test_value4", "field5": "test_value5"}'
```

3. The response received will be a successful one, since we attempted to send fewer fields in the POST request payload.

![image alt text](./media/success-response.png)

We also see that the JSON Threat Protection policy allowed the request to go through and hit the API target, when we see the Trace screen.

![image alt text](./media/success-response-trace.png)

## XML Threat Protection

1. Once again, click on the "**Send request and view request headers and body**" flow under Proxy Endpoints default, and then click on **+Step** on the upper right of the Request flow to attach a XML Threat Pretection policy this time.

![image alt text](./media/select-xml-flow-for-policy.png)

2. Select **XML Threat Protection** policy. Click on **Add** button to add the policy to the selected flow's request pipeline.

![image alt text](./media/add-xml-threat-policy.png)

3. Select the policy to display the policy's XML configuration in the editor.

![image alt text](./media/select-xml-threat-policy-config.png)

4. Change the policy's XML configuration to the below snippet to enforce protection against XML payload manipulation threats.
```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<XMLThreatProtection async="false" continueOnError="false" enabled="true" name="XML-Threat-Protection-1">
    <DisplayName>XML Threat Protection-1</DisplayName>
    <Source>request</Source>
    <StructureLimits>
        <ChildCount includeElement="true" includeText="true">3</ChildCount>
    </StructureLimits>
</XMLThreatProtection>
```

In the above example, we use the XML Threat Protection policy to ensure that the incoming API request XML payload does not contain more than 3 child nodes for any element. If the incoming payload contains more than 3 child nodes for any of the elements, the API proxy returns an error response.
For a full list of XML payload integrity checks that can be performed using this policy, see the [XML Threat Protection policy documentation](https://docs.apigee.com/api-platform/reference/policies/xml-threat-protection-policy#elementreference).

5. Click on **Save** to save the API Proxy changes.

![image alt text](./media/save-changes-2.png)

### To Test XML Threat Protection:

1. To test the changes made, first click on **Trace** tab of the API proxy dashboard, and click on **Start Trace Session** button.

![image alt text](./media/start-trace.png)

2. Now, send a POST request to the API endpoint **http://{{your-organization}}-{{your-environment}}.apigee.net/mock-target-api/echo** with the following format:
```
POST /mock-target-api/echo HTTP/1.1
Host: {{your-org}}-{{your-env}}.apigee.net
Content-Type: application/xml

<body>
  <node1>value1</node1>
  <node2>value2</node2>
  <node3>value3</node3>
  <node4>value4</node4>
</body>
```

You can make this call either using a REST client like the one [here](https://apigee-rest-client.appspot.com/), or using a terminal command such as **curl**.
```
curl -X POST "http://{{your-org}}-{{your-env}}.apigee.net/mock-target-api/echo" -H "Content-Type: application/xml" -d '<body><node1>value1</node1><node2>value2</node2><node3>value3</node3><node4>value4</node4></body>'
```

* **Note:** If you are using a REST client, make sure that your HTTP request has a Header name/value pair of `Content-Type: xml/json` as shown below

![image alt text](./media/add-xml-header.png)

3. The response received will be an error, since we attempted to send more than 3 child nodes under element '<body>' in the POST request payload.

![image alt text](./media/error-response-xml-threat.png)

We also see that the XML Threat Protection policy was triggered to return this error response, when we see the Trace screen.

![image alt text](./media/error-response-xml-threat-trace.png)

4. You can now test for a successful API call, by sending the API endpoint a similar POST request, but this time with 3 or fewer child nodes for each element in the XML payload.
```
POST /mock-target-api/echo HTTP/1.1
Host: org-env.apigee.net
Content-Type: application/xml

<body>
  <node1>value1</node1>
  <node2>value2</node2>
  <node3>value3</node3>
</body>
```

You can make this call either using a REST client like the one [here](https://apigee-rest-client.appspot.com/), or using a terminal command such as **curl**.
```
curl -X POST "http://{{your-org}}-{{your-env}}.apigee.net/mock-target-api/echo" -H "Content-Type: application/xml" -d '<body><node1>value1</node1><node2>value2</node2><node3>value3</node3></body>'
```

3. The response received will be a successful one, since we attempted to send fewer fields in the POST request payload.

![image alt text](./media/success-response-xml-threat.png)

We also see that the XML Threat Protection policy allowed the request to go through and hit the API target, when we see the Trace screen.

![image alt text](./media/success-response-xml-threat-trace.png)

# Lab Video

If you like to learn by watching, here is a [short video](https://youtu.be/rC8kZJgwBFM) on using Threat Protection policies in Apigee Edge.

# Earn Extra-points

Now that you have tried the JSON and XML Threat Protection policies, try out the [Regular Expression Protection policy](https://docs.apigee.com/api-platform/reference/policies/regular-expression-protection) that helps you check the API payload content integrity using regular expression checks. Also, here's [a short video](https://youtu.be/NLg_LE3u-vM) on using this policy.

#Summary

That completes this hands-on lesson. In this simple lab you learned how to protect your API payloads against payload content based threats.

# References

* Useful Apigee documentation links on Threat Protection policies  - 

    * [JSON Threat Protection Policy](https://docs.apigee.com/api-platform/reference/policies/json-threat-protection-policy)

    * [XML Threat Protection Policy](https://docs.apigee.com/api-platform/reference/policies/xml-threat-protection-policy)

    * [Regular Expression Protection policy](https://docs.apigee.com/api-platform/reference/policies/regular-expression-protection) 

Now go to [Lab-3](https://github.com/aliceinapiland/AdvancedVirtualAPIJam/tree/master/SecurityJam/Lab%203%20-%20Securing%20APIs%20with%20OAuth2%20Client%20Credentials)
