---
title: Everything, Browser Security has to offer
date: 2024-06-18 00:13:57 +0530
categories: [Blogs]
tags: [Browser Security, CORS, SOP, SameSite, CSP]
image: /images/browser_security.png
---


A significant proportion of Internet traffic is malicious. In this blog, I will try to cover all the essential browser security features that most of the people are not aware about. 

## SameSite Attribute

It is a security feature, that is applied to cookies. It controls whether a cookie is sent with Cross-Site Requests.<br>
So **What are Cross-Site Requests ??** <br>
*  These are requests, where the domain OR the scheme (https, http) of the site doesn't match the site making the request.
A SameSite attribute can have 3 values - Strict, Lax, None

1. SameSite=Strict
    - When the `SameSite` attribute is set to Strict, cookies will only be sent if the request originates from the same site as the cookie.
    - It ensures that cookies are not sent with any requests initiated by third-party websites, and hence effectively preventing CSRF attacks
    ```
    Set-Cookie: sessionId=abc123; SameSite=Strict
    ```
    
2. SameSite=Lax
    - When the `SameSite` attribute is set to Lax, cookies are sent when the user navigates to the URL, but are not sent on normal cross-site requests, e.g. loading images or iframes.
    - This setting provides a balance b/w security and usability.
    ```
    Set-Cookie: sessionId=abc123; SameSite=Lax
    ```

3. SameSite=None
    - In this case, cookies are sent in all contexts, including cross-site requests.
    - However, with this setting, cookies must also have the `secure` attribute, which means they are only sent over HTTPS.
    ```
    Set-Cookie: sessionId=abc123; SameSite=None; Secure
    ```

## Same Origin Policy (SOP)

It is a security measure, implemented in web browsers to protect against malicious behaviour. It restricts web pages from making requests to a different origin.<br>
An origin is defined by the combination of URL scheme, domain, and port number. <br>
For example - <br>
*  http://example.com:80/page.html
*  http://example.com:8080/page.html
*  https://example.com:80/page.html
   
   All are from different origin due to differences in scheme and port numbers.

Key restrictions by SOP include - 
1. Scripts from one origin cannnot access or manipulate the DOM from other origin.
2. Cookies are restricted to the origin that set them, and can't be read by other origins.
3. XMLHttpRequest and Fetch API requests to different origins are blocked unless the server explicitly allows it via Cross-Origin Resource Sharing (CORS).

## Cross Origin Resource Sharing (CORS)

CORS is a protocol, which allows servers to specify who can access their resources and which HTTP methods are permitted.<br>
They do this, by including specific HTTP Headers, which can allow cross-origin requests.<br>
Key CORS headers: 
1. Access-Control-Allow-Origin:
    - Specifies which origins can access the resource
    ```
    Access-Control-Allow-Origin: https://example.com
    ```
    - To allow all origin it can simply set it to a wildcard(*)
    ```
    Access-Control-Allow-Origin: *
    ```
2. Access-Control-Allow-Methods:
    - It lists the HTTP methods that are allowed when accessing the resource
    ```
    Access-Control-Allow-Methods: GET, POST, PUT
    ```
3. Access-Control-Allow-Headers:
    - It indicates which headers can be used in the request, originating from a different origin
    ```
    Access-Control-Allow-Headers: Content-Type, Authorization
    ```
4. Access-Control-Allow-Credentials:
    - It indicates, whether the browser should expose the response to the front-end JS code, when the credentials flag is true.
    ```
    Access-Control-Allow-Credentials: true
    ```
5. Access-Control-Max-Age:
    - It specifies, for how long the results of the previous requests can be cached.
    ```
    Access-Control-Max-Age: 76400
    ```
6. Access-Control-Expose-Headers:
    - It indicates, which headers can be exposed as a part of the response.
    ``` 
    Access-Control-Expose-Headers: X-Custom-Header
    ```

