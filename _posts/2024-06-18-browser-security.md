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

## Content Security Policy (CSP)

CSP allows web developers to control what resources a webpage loads and executes. CSP reduces the risk of various web vulnerabilities like XSS, CSRF etc. <br>
It is implemented by setting HTTP headers that define allowed sources for different types of content on web pages.<br>
Key CSRF policies:
1. default-src
    - This policy restricts all content to be loaded only from the same origin
    ```
    Content-Security-Policy: default-src 'self'
    ```
2. script-src
    - It defines valid sources for Javascript.
    - This policy allows JS to be loaded only from the same origin and any other origin mentioned.
    ```
    Content-Security-Policy: script-src 'self' https://trusted.cdn.com
    ```
3. style-src
    - It defines valid sources for stylesheets.
    - This policy allows stylesheets to be loaded only from the same origin and any other origin mentioned.
    ```
    Content-Security-Policy: style-src 'self' https://trusted.styles.com
    ```

## HTTP Strict Transport Security (HSTS)

It is a web security policy that helps protect websites from man-in-the middle attacks such as protocol downgrade and cookie hijacking.<br>
It makes the web browsers, or other user agents interact with servers using secure HTTPS connections.
```
Strict-Transport-Security: max-age=31536000; includeSubDomains
```
- `max-age` specifies the time in seconds, that the browser should remember that the site is only accessible over HTTPS.
- `includeSubDomains` is optional, and if specified, states that the rule applies to the enter domain and all its subdomains.
- Another optional directive that is used is `preload` which states that the domain should be included in browsers' HSTS preload lists, which means that the domain will be treated as an HSTS host by default.



If you found this article helpful, consider subscribing to my YouTube channel, [The hacker's room](https://www.youtube.com/@TheHackersRoom) for more insights on web security and other cybersecurity topics. Let's continue learning and improving our security practices together!
