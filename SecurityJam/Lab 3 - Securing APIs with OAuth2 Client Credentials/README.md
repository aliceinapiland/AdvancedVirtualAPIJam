# **API Security : Securing APIs with 2-legged OAuth (client credentials)**

*Duration : 30 mins*

*Persona : API Team/Security*

# **Use case**

You have an API that is consumed by trusted applications. You want to secure that API using two legged OAuth (client credentials grant type).

# **How can Apigee Edge help?**

Apigee Edge quickly lets you secure your APIs using out of the box OAuth policies. OAuth defines token endpoints, authorization endpoints, and refresh endpoints. Apps call these endpoints to get access tokens, to refresh access tokens, and, in some cases, to get authorization codes. These endpoints refer to specific OAuth 2.0 policies that execute when the endpoint is called.

Most typically, the client_credentials grant type is used when the app is also the resource owner. For example, an app may need to access a backend cloud-based storage service to store and retrieve data that it uses to perform its work, rather than data specifically owned by the end user. This grant type flow occurs strictly between a client app and the authorization server. An end user does not participate in this grant type flow. In this flow, Apigee Edge is the OAuth authorization server. Its role is to generate access tokens, validate access tokens, and pass authorized requests for protected resources on to the resource server.

# **Pre-requisites**

* You have completed [Lab 1](https://github.com/aliceinapiland/AdvancedVirtualAPIJam/tree/master/SecurityJam/Lab%201%20Traffic%20Management%20-%20Throttle%20APIs). If not, please complete that first.

# **Instructions**

* Go to [https://apigee.com/edge](https://apigee.com/edge) and log in. This is the Edge management UI.

* Select Develop → API Proxies in the side navigation menu.

![image alt text](./media/image_0.png)

* Click the +Proxy button on the top-right corner to invoke the Create Proxy wizard.

![image alt text](./media/image_1.png)

* Select Proxy Bundle and then click Next to import an existing proxy form a zip archive.

![image alt text](./media/image_2.png)

* Download the Apigee proxy "oauth.zip" that implements OAuth client credentials grant type [here](https://github.com/aliceinapiland/AdvancedVirtualAPIJam/blob/master/SecurityJam/Lab%203%20-%20Securing%20APIs%20with%20OAuth2%20Client%20Credentials/oauth.zip?raw=true).  Then click "Choose File", select the “oauth.zip” file you just downloaded and click Next:

![image alt text](./media/image_3.png)

* Click Build:

![image alt text](./media/image_4.png)

* You should see a successful "Uploaded proxy" message as shown below.  You now have an OAuth Authorization Server that supports the client credentials grant type in Apigee.  Click “oauth” (not Exit! To be fixed):

![image alt text](./media/image_5.png)

* Deploy the oauth proxy by clicking on the Deployment dropdown and selecting test:

![image alt text](./media/image_6.png)

* Select Develop → API Proxies in the side navigation menu:

![image alt text](./media/image_7.png)

* Select the Mock-Target-API proxy:

![image alt text](./media/image_8.png)

* Click on the **Develop** tab:

![image alt text](./media/image_9.png)

* Ensure that "Preflow" is selected in the “Proxy Endpoints” window, and then click the “+Step” button above the “Request” flow:

![image alt text](./media/image_10.png)

* Select the "OAuth v2.0" security policy, leave the default names, and then click “Add”:

![image alt text](./media/image_11.png)

* Drag and drop the OAuth v2.0 policy so it is the first policy (before Spike Arrest) and then click "Save".  After the proxy is saved, click “Trace” in the upper right:

![image alt text](./media/image_12.png)

* Click "Start Trace Session" and then click “Send”:

![image alt text](./media/image_13.png)

* You should see a 401 error because the proxy is now protected with an OAuth v2.0 policy and the incoming http request to the proxy did not contain an OAuth bearer token.  So now we will need to get a valid OAuth token in order to proceed.  This will require registering a **Developer** who creates an **App** that uses an **API Product** that contains the **API Proxy**.

* Let’s first create an **API Product **and add the the **Mock-Target-API Proxy** to it. Click on Publish→ API Product:

![image alt text](./media/image_14.png)

* Then click **+API Product** in the upper right of the screen:

![image alt text](./media/image_15.png)

* Fill out the fields as shown below.  Click +API Proxy (step 4) and then select the Mock-Target-API (step 5) from the dropdown.  Finally click Save :

![image alt text](./media/image_16.png)

* You should now see the Mock Target Product in the list of API Products.  

* Click on Publish → Developer:

![image alt text](./media/image_17.png)

* Click on +Developer in the upper right of the screen:![image alt text](image_18.png)

* Fill out the fields with your **own** name and email address and click **Create**:

![image alt text](./media/image_19.png)

* You should see the new Developer you just created in the list.  

* Click on Publish → Apps

![image alt text](./media/image_20.png)

* Click on +App in the upper right of the screen:

![image alt text](./media/image_21.png)

* Fill out the details in the App screen as shown below.  Click Save:

![image alt text](./media/image_22.png)

* You will now see your list of Apps again.  Click on your **Mock Target App** again and click the "Show/Hide" buttons next to the **Consumer Key **and **Consumer Secret** fields.  Copy the Consumer Key and Consumer Secret so you can use them later.  These are the client credentials you will need to get your OAuth token:

![image alt text](./media/image_23.png)

* Open up a new browser tab and go to [https://apigee-rest-client.appspot.com/](https://apigee-rest-client.appspot.com/)

* Change the HTTP verb to POST, and enter your URL to your OAuth server:

[https://](https://{your-org-name}-test.apigee.net/oauth/client_credential/accesstoken?grant_type=client_credentials)[{your-org-name}](https://{your-org-name}-test.apigee.net/oauth/client_credential/accesstoken?grant_type=client_credentials)[-test.apigee.net/oauth/client_credential/accesstoken?grant_type=client_credentials](https://{your-org-name}-test.apigee.net/oauth/client_credential/accesstoken?grant_type=client_credentials)

* Replace  [{your-org-name}](https://{your-org-name}-test.apigee.net/oauth/client_credential/accesstoken?grant_type=client_credentials) with your actual Apigee org name.

* Click on Body, select x-www-form-url-encoded.

* Enter the client_id and client_secret and substitute your real Consumer Key and Consumer Secret accordingly.

* Click Send.  Your screen should look like this:

![image alt text](./media/image_24.png)

* You now have an OAuth access token as seen in the body of the HTTP response.  Copy the value of the access_token (not including the " “) as you will need it for the next step.

* Change the POST back to GET, and enter your URL to the Mock-Target-API that is now protected by OAuthV2:

[http://](http://demo32-test.apigee.net/mock-target-api)[{your-org-name}](https://{your-org-name}-test.apigee.net/oauth/client_credential/accesstoken?grant_type=client_credentials)[-test.apigee.net/mock-target-api](http://demo32-test.apigee.net/mock-target-api)

* Substitute [{your-org-name}](https://{your-org-name}-test.apigee.net/oauth/client_credential/accesstoken?grant_type=client_credentials) with your actual Apigee org name.

* Add a header named **Authorization**, and in the value field write **Bearer** followed by your **access_token** you copied after your last POST request.  Finally click Send and you should see the following:

![image alt text](./media/image_25.png)

* If you see "Hello, Guest!" your OAuth token was valid and you’ve received the correct response!  

# **Lab Video**

If you are lazy and don’t want to implement this use case, it’s OK. You can watch this short video to see how to implement 2 legged OAuth on Apigee Edge [https://youtu.be/0pah5J7yQTQ](https://youtu.be/0pah5J7yQTQ)

# **Earn Extra-points**

Now that you’ve learned how to secure your API with OAuth 2.0, try to control the expiry of the access token that is generated.

# **Quiz**

1. What are the various OAuth 2.0 grant types supported by Apigee Edge?

2. What are the various operations that are provided by the OAuth v2.0 policy?

# **Summary**

In this lab you learned how to secure your API using a two legged OAuth by using the default oauth proxy obtaining an access code and using that against your API.

# **References**

* Link to Apigee docs page

    * OAuth 2.0: Configuring a new API proxy [http://docs.apigee.com/api-services/content/understanding-default-oauth-20-configuration](http://docs.apigee.com/api-services/content/understanding-default-oauth-20-configuration)

    * Secure an API with OAuth [http://docs.apigee.com/tutorials/secure-calls-your-api-through-oauth-20-client-credentials](http://docs.apigee.com/tutorials/secure-calls-your-api-through-oauth-20-client-credentials)

* [Link](https://community.apigee.com/topics/oauth+2.0.html) to Community posts and articles with topic as "OAuth 2.0"

* Search and Revoke tokens - [https://community.apigee.com/articles/1571/how-to-enable-oauth-20-token-search-and-revocation.html](https://community.apigee.com/articles/1571/how-to-enable-oauth-20-token-search-and-revocation.html)

# **Rate this lab**

How did you link this lab? Rate [here](https://drive.google.com/open?id=1L95jU79wmOP-rHVY2Laba8lApZpS-yztwdONz0nCzWs).

