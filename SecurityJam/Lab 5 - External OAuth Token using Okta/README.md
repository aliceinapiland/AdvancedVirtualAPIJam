# **API Security - External IdP Integration using Okta**

*Duration : 20 mins*

*Persona : API Team/Security*

# **Use case**

You have an API that is consumed by a client application. You want to secure that API using OAuth 2.0 and use an external identiy provider such as Okta, to protect the application end user identity.
In this lab, we will use Apigee as the OAuth provider to protect the API endpoints using OAuth 2.0. Okta will be used to protect the application end user's identity. We will accomplish this by integrating Okta into the Apigee OAuth proxy, and implement OAuth 2.0 in resource owner / password grant type.

# **How can Apigee Edge help?**

See: [Apigee + Okta - Using OAuth 2.0 Resource Owner / Password Grant Type](https://community.apigee.com/articles/28752/apigeeokta-integration-resource-owner-password-gra.html)
Apigee has built in support to implement OAuth 2.0 in the resource owner / password grant type. Using the [OAuthV2 policy](https://docs.apigee.com/api-platform/reference/policies/oauthv2-policy), Apigee Edge can be configured to act as the authorization provider for access to the API, while using the [Service Callout policy](https://docs.apigee.com/api-platform/reference/policies/service-callout-policy) to invoke Okta's authentication API to authenticate the identity of the app end user.

# **Pre-requisites**

* You have completed [Lab 1](https://github.com/aliceinapiland/AdvancedVirtualAPIJam/tree/master/SecurityJam/Lab%201%20Traffic%20Management%20-%20Throttle%20APIs). If not, please complete that first.

# **Instructions**

Let us assume that there is a client application that needs to consume the API endpoints we built in the previous labs - **Mock-Target-API**, and that this application is a trusted one.
The resource owner password (or "password") grant type is mostly used in cases where the app is highly trusted. In this configuration, the user provides their resource server credentials (username/password) to the client app, which sends them in an access token request to Apigee Edge. An identity server (it this case, Okta) validates the credentials, and if they are valid, Edge proceeds to mint an access token and returns it to the app.

In this scenario, let us proceed to set up
a) The app end user's identity in Okta, 
b) The app configuration in Apigee Edge, and
b) The API proxy configuration in Apigee Edge to enforce both end user identity authentication, as well as API authorization through OAuth 2.0.

## End User Configuration in Okta

1. In this lab, we will use a pre-configured Okta instance to authenticate end user identity. To add a new app end user, we will use the Okta User API.
Invoke the following API request (either from a terminal or [REST client](https://apigee-rest-client.appspot.com/)):
```
curl -X POST "https://googlevidya-apigee-sample-admin.okta.com/api/v1/users?activate=true" -H "Content-Type: application/json" -H "Authorization: SSWS 00gQdAVXg9db3Wx2UfXsAfECy8wdyHLEu3ePDFu7Cp" -d '{"profile": {"firstName": "UserFirstName","lastName": "UserLastName","email": "useremail@test.com","login": "useremail@test.com"},"credentials": {"password" : { "value": "Passwordvalue123"}}}'
```

![image alt text](./media/RESTClient-Okta-User-API-Request1.png)
![image alt text](./media/RESTClient-Okta-User-API-Request2.png)

This will create an active end user profile in Okta:
![image alt text](./media/RESTClient-Okta-User-API-Response.png)
![image alt text](./media/Okta-User-Created.png)

2. Make note of the Username and Password used in the above API request. We will use this to authenticate the app end user's identity.

## App Configuration in Apigee Edge

1. To provide access to the API, we must first package the API proxy into an API Product. To do this, first log into the Apigee Edge Management UI, and navigate to **Publish -> API Products**:
![image alt text](./media/Publish-APIProducts.png)

2. Click the **+API Product** button.
![image alt text](./media/AddAPIProduct.png)

Provide the API Product configuration as shown below, and click **Save**:
![image alt text](./media/AddAPIProductDetails.png)

3. Typically, the client app developer will register his/her profile and the app profile, to obtain app credentials through a developer portal. However, for this lab, we will create these entities through the Apigee Edge Management UI. 

First let's create the developer profile. To do this, navigate to **Publish -> Developers** on the Management UI:
![image alt text](./media/Publish-Developers.png)

4. Click the **+Developer** button.
![image alt text](./media/AddDeveloper.png)
Provide the Developer configuration as shown below, and click **Create**:
![image alt text](./media/AddDeveloperDetails.png)

5. Navigate to **Publish -> Apps** on the Management UI:
![image alt text](./media/Publish-Apps.png)

6. Click the **+App** button.
![image alt text](./media/AddApp.png)

Provide the app configuration as shown below, and click **Save**:
![image alt text](./media/AddAppDetails.png)

7. Navigate to **Publish -> Apps** on the Management UI, search for and click on the newly created App:
![image alt text](./media/SearchApp.png)

Click on the show button next to the Client Key and Client Secret for the app, as shown below, and make a note of both. We will use these app credentials to generate an OAuth access token.
![image alt text](./media/SelectClientCreds.png)

## Enforce security in the API proxy

1. First, we must set up the OAuth token endpoint. To do this, download the API proxy bundle from [here](https://github.com/aliceinapiland/AdvancedVirtualAPIJam/raw/master/SecurityJam/Lab%205%20-%20External%20OAuth%20Token%20using%20Okta/resources/oauth-okta-integration.zip).

2. Once downloaded, navigate to **Develop -> API Proxies** in the Apigee Edge Management UI:
![image alt text](./media/Develop-APIProxies.png)

3. Click the **+Proxy** button.
![image alt text](./media/AddProxy.png)

4. In the proxy creation wizard, select the **Proxy Bundle** option and click **Next**.
![image alt text](./media/ProxyBundleOption.png)

5. On the next screen, click **Choose File** and upload the previously downloaded proxy bundle zip. Then click **Next**.
![image alt text](./media/ChooseProxyBundle.png)

6. On the next screen, click **Build** to build the proxy.
![image alt text](./media/BuildProxyBundle.png)

7. Confirm that the proxy was uploaded successfully and click on the view proxy link:
![image alt text](./media/ViewProxyBundle.png)

8. On the Proxy Overview page, click the **Deployment** button, and select the **test** environment. Click **Deploy** in the confirmation pop-up.
![image alt text](./media/DeployProxyBundle.png)
![image alt text](./media/DeployProxyBundleConfirm.png)

9. Navigate to **Develop -> API Proxies** in the Apigee Edge Management UI:
![image alt text](./media/Develop-APIProxies.png)

10. In the API Proxy list, search and select the **Mock-Target-API** proxy:
![image alt text](./media/SearchAPIProxy.png)

11. On the proxy overview screen, click the **Develop** tab:
![image alt text](./media/ProxyDevelopTab.png)

12. In the proxy develop screen, select the **PreFlow** from the menu on the left:
![image alt text](./media/SelectPreFlow.png)

13. Click the **+Step** button on the request pipline of the PreFlow, as shown below:
![image alt text](./media/AddStep.png)

From the pop-up menu, select the OAuth v2.0 policy and click **Add** as shown below:
![image alt text](./media/AddOAuthPolicy.png)

Select the policy in the flow and edit the policy's XML configuration as shown below:
![image alt text](./media/OAuthPolicyConfig.png)

Then, click **Save**.
![image alt text](./media/SaveProxy.png)

## Test

Now that we have configured the end user credentials in Okta, and the API Proxy and App credentials in the Apigee Edge, let us proceed to test the OAuth resource owner / password flow end to end.

1. (Optional) Navigate to the proxy overview screen of the "oauth-okta-integration" proxy and  start the **Trace** session:
![image alt text](./media/StartTraceOAuthProxy.png)

2. Send the following token generation request to the access token endpoint, using a terminal or a [REST client](https://apigee-rest-client.appspot.com):
```
curl -X POST -H "Accept:application/json" -H "Content-Type:application/x-www-form-urlencoded" -d 'grant_type=password&user={{okta_user}}&password={{okta_password}}&client_id={{client_id}}&client_secret={{client_secret}}' "https://{{org}}-{{env}}.apigee.net/oauth-ext/token"
```

![image alt text](RESTClient-OAuthRequest1.png)
![image alt text](RESTClient-OAuthRequest2.png)

Note down the generated access token:
![image alt text](RESTClient-OAuthResponse.png)

Also, note in the Trace session that the Service Callout policy in the "oauth-okta-integration" proxy is called to validate the end user identity in Okta. On successful authentication, the proxy uses the OAuthV2 policy to generate the access token.
![image alt text](TraceResultOAuthProxy.png)

3. Now, let us test the "Mock-Target-API" proxy which we have now protected with the OAuthV2 policy.
(Optional) Navigate to the proxy overview screen of the "Mock-Target-API" proxy, and start the Trace session:
![image alt text](./media/StartTraceProxy.png)

4. Send in a request to the API Proxy without the authorization:
```
curl -X GET "http://{{org}}-{{env}}.apigee.net/mock-target-api"
```

Notice that an error response is returned since the access token was not sent in the request:
![image alt text](./media/RESTClient-ProxyResponse.png)

5. Now, send in an API request with the access token in the Authorization header:
```
curl -X GET -H "Authorization:Bearer NekHnzn7wPYIu4kkmlVbK1BcdWQE" "http://{{org}}-{{env}}.apigee.net/mock-target-api"
```

Once the access token is validated, a successful API response is returned:
![image alt text](./media/RESTClient-ProxyResponseSuccess.png)