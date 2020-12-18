# looping-example
### Scenario:
You have a single API that needs to be able to separately support requests using both OAuth 2.0 and Authentication Tokens for authentication.

### How to Solve With Tyk
We’re going to create 3 apis in Tyk for this effort.  The api definitions have been attached, you can import these using the Dashboard UI ([instructions](https://tyk.io/docs/tyk-configuration-reference/import-apis/#import-apis-via-the-dashboard)).

First, we’re going to set up the apis for Authorization Token and Oauth 2.0 respectively.  Both API’s are protecting the http://httpbin.org endpoint.

#### HttpBin - OAuth 2.0
https://meaningful-shaker-mgw.aws-use1.cloud-ara.tyk.io/httpbin-oauth-2.0/


#### HttpBin - Auth Token
https://meaningful-shaker-mgw.aws-use1.cloud-ara.tyk.io/httpbin-auth-token/


To avoid having to share two different urls for your users who authenticate via Oauth vs. Auth Token, we will create a third api that both groups of users will use as the ingress.  This will be the only url that needs to be shared with your api consumers.

#### HttpBin - Ingress
https://meaningful-shaker-mgw.aws-use1.cloud-ara.tyk.io/httpbin-ingress/


For the “HttpBin - Ingress” api we’re going to use the URL rewrite plugin along with our looping feature to direct OAuth 2.0 authenticated requests to the Oauth 2.0 endpoint and Auth Token requests to the respective Auth Token endpoint.

In Endpoint designer, we want to add the URL Rewrite plugin and apply the following settings:

(Insert URL Rewrite Image)


What this setup does is as follows, Tyk will examine the Authorization header and look for the presence of the word “Bearer”.  If “Bearer” is present then we know it's an OAuth 2.0 request and we need to route the request to the Oauth endpoint.  If not, we know to route this request to the Auth Token endpoint.

Note: We’re using the api id to reference the OAuth 2.0 and Auth Token apis.  We could have used the api urls themselves instead of the api ids but this method saves us a hop.  Instead of having to send the request out to the internet which will require a dns lookup, we can send the request directly to the target api. 

(Insert API Header Image)
