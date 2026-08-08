Absolutely bro. This is worth keeping as a GitHub revision note. I've made it concise enough to revise quickly, but detailed enough that you can reconstruct today's mental model.

DevOps Troubleshooting — Day 1

Process → Socket → Port → TCP → HTTP

«Goal: Build an engineering mental model for troubleshooting an application from the inside out instead of blindly running commands.»

---

1. Application Process

Before troubleshooting network communication, first verify that the application process is actually running.

Command

ps -ef | grep <application-name>

What does "ps" tell us?

"ps" displays information about running processes.

Example:

appuser   12345  1  0 10:20 ?  00:00:05 java -jar orders-api.jar

Important information:
```
12345
  ↓
PID (Process ID)

Mental model

Is the application process running?
        ↓
       ps

If the process is not running, investigate:
```
- Application startup failure
- Configuration error
- Missing dependency
- Crash
- Out-of-memory
- Service failure

---

2. Port and Socket

An application needs a network endpoint where it can accept connections.

A port identifies a network service endpoint on a machine.

A socket represents the communication endpoint used by the operating system.

For a server application:
```
Application
     ↓
OS / Kernel
     ↓
Socket
     ↓
Port
     ↓
LISTEN
```
---

3. TCP

TCP = Transmission Control Protocol

TCP provides a reliable communication channel between two endpoints.

For a server:
```
Application
    ↓
Creates/binds socket
    ↓
OS assigns/uses TCP port
    ↓
Socket enters LISTEN state
    ↓
Client connects
    ↓
TCP connection established

Important concept

HTTP does not replace TCP.

HTTP data travels over a TCP connection in traditional HTTP/1.1 and HTTP/2 over TCP.

Mental model:

HTTP
  ↓
TCP
  ↓
IP
  ↓
Network
```
---

4. Checking Listening Ports

Command

ss -lntp

Useful options:

-l → listening sockets
-n → show numeric addresses/ports
-t → TCP sockets
-p → show process information

Example:

LISTEN 0 128 0.0.0.0:8080 0.0.0.0:* users:(("java",pid=12345,fd=123))

This tells us that:

Application → java
PID         → 12345
Protocol    → TCP
State       → LISTEN
Port        → 8080
Address     → 0.0.0.0

Troubleshooting question
```
«Is the application listening on the expected TCP port?»

If there is no listening socket on the expected port:

Process may be running
        ↓
BUT
        ↓
Application may not have successfully bound/listened
        ↓
Investigate application configuration/startup
```
---

5. "127.0.0.1" vs "0.0.0.0"

This is an important troubleshooting concept.

"127.0.0.1"

Loopback/local machine only.

Client on same machine
        ↓
127.0.0.1
        ↓
Application

Other machines generally cannot reach an application listening only on "127.0.0.1".

"0.0.0.0"

Means the application is listening on all IPv4 interfaces (subject to firewall/network rules).

Other clients
      ↓
Network interface
      ↓
0.0.0.0:8080
      ↓
Application

---

6. "curl"

"curl" is a command-line tool used to make network requests.

For HTTP troubleshooting:

curl http://localhost:8080/orders

The important difference:
```
ps
 ↓
Checks process

ss
 ↓
Checks listening socket

curl
 ↓
Actually sends an HTTP request
```
Mental model
```
Process?
   ↓
  ps

Listening?
   ↓
  ss

Can I communicate with the application using HTTP?
   ↓
  curl
```
---

7. HTTP

HTTP = HyperText Transfer Protocol

HTTP defines how a client and server communicate using requests and responses.

Basic flow:
```
CLIENT
   │
   │ HTTP REQUEST
   ▼
SERVER / APPLICATION
   │
   │ HTTP RESPONSE
   ▼
CLIENT
```
---

8. HTTP Request

An HTTP request contains:
```
HTTP Request
│
├── Request line
├── Headers
└── Body
```
The request line contains:

METHOD /path HTTP-version

Example:

GET /orders HTTP/1.1

---

9. HTTP Methods
```
GET

Retrieve data.

GET /orders

POST

Create/send data.

POST /orders

PUT

Replace/update a resource.

PUT /orders/123

PATCH

Partially update a resource.

PATCH /orders/123

DELETE

Delete a resource.

DELETE /orders/123
```
Mental model:
```
GET     → Read
POST    → Create/Submit
PUT     → Replace/Update
PATCH   → Partial update
DELETE  → Delete
```
---

10. URL Structure

Example:

https://api.company.com:443/orders/123?status=active&limit=10

Breakdown:
```
https://
   ↓
Scheme

api.company.com
   ↓
Host

:443
   ↓
Port

/orders/123
   ↓
Path

?status=active&limit=10
   ↓
Query parameters

Path

/orders/123

Usually identifies a particular resource.

Query parameters

/orders?status=active&limit=10

Usually provide filtering/options for the request.
```
---

11. HTTP Headers

Headers provide additional information about an HTTP request or response.

Request headers

Example:
```
Host: orders.company.com
Authorization: Bearer <token>
Content-Type: application/json
Accept: application/json
```
Response headers

Example:
```
Content-Type: application/json
Content-Length: 250
```
Important distinction

Request headers
      ↓
Client → Server

Response headers
      ↓
Server → Client

Headers are not only present in requests.

They exist in both HTTP requests and responses.

---

12. Important HTTP Headers

Host

Host: orders.company.com

Identifies the hostname being requested.

Authorization

Authorization: Bearer <token>

Carries authentication credentials/token information.

Later we'll study:

Authentication
Authorization
Tokens
JWT
OAuth
RBAC

Content-Type

Tells the server the format of the request body.

Content-Type: application/json

Meaning:

«The body contains JSON.»

Accept

Tells the server what response format the client prefers.

Accept: application/json

Important distinction:

Content-Type
    ↓
What am I sending?

Accept
    ↓
What do I want to receive?

---

13. HTTP Request Body

The body contains data sent by the client.

Example:
```
POST /orders HTTP/1.1
Content-Type: application/json

{
  "product": "Laptop",
  "quantity": 1
}

The body:

{
  "product": "Laptop",
  "quantity": 1
}
```
The "Content-Type" tells the application how to interpret the body.

---

14. Sending an HTTP Body with curl

Example:
```
curl -X POST http://localhost:8080/orders \
  -H "Content-Type: application/json" \
  -d '{"product":"Laptop","quantity":1}'
```
Meaning:
```
-X POST
   ↓
HTTP method

-H
   ↓
Add header

Content-Type: application/json
   ↓
Body format

-d
   ↓
Data/body
```
---

15. HTTP Response

The server sends an HTTP response back to the client.

Example:
```
HTTP/1.1 200 OK
Content-Type: application/json

{
  "orders": [...]
}
```
An HTTP response contains:
```
HTTP Response
│
├── Status line
├── Response headers
└── Response body
```
The status line:

HTTP/1.1 200 OK

contains:

HTTP version → HTTP/1.1
Status code → 200
Reason phrase → OK

---

16. HTTP Status Codes Learned

We have started building the mental model around HTTP status codes.
```
200

200 OK

Request succeeded.

401

401 Unauthorized

Authentication is required/failed.

Think:

«"Who are you?"»

403

403 Forbidden

The request is understood, but access is not permitted.

Think:

«"I know who you are, but you're not allowed to do this."»

404

404 Not Found

The requested resource/route was not found.

500

500 Internal Server Error

The server/application encountered an internal error.

502

502 Bad Gateway

A gateway/proxy received an invalid response from an upstream service.

504

504 Gateway Timeout

A gateway/proxy waited too long for an upstream response.
```
«We will study all major status codes properly in a separate HTTP troubleshooting module.»

---

17. The Troubleshooting Mental Model

This is the most important thing learned today.

Don't start with:

«"What command should I run?"»

Start with:

«"Which layer do I need to prove?"»

Basic troubleshooting flow:
```
Application not working
        │
        ▼
Is process running?
        │
       ps
        │
        ▼
Is application listening?
        │
       ss
        │
        ▼
Is TCP communication possible?
        │
        ▼
Can HTTP reach the application?
        │
       curl
        │
        ▼
What HTTP response did I receive?
        │
        ▼
Investigate the appropriate layer
```
---

18. End-to-End Mental Model

The complete model we built today:
```
                    CLIENT
                       │
                       │
                 HTTP REQUEST
                       │
              ┌────────┴────────┐
              │                 │
           Method              URL
                              │
                    ┌─────────┼─────────┐
                    │         │         │
                  Host       Port      Path
                              │
                         Query Params
              │
           Headers
              │
            Body
              │
              ▼
        ┌─────────────────┐
        │   APPLICATION   │
        │                 │
        │   Process       │
        │      ↓          │
        │   Socket        │
        │      ↓          │
        │   TCP           │
        │      ↓          │
        │   HTTP          │
        └────────┬────────┘
                 │
                 ▼
            HTTP RESPONSE
                 │
        ┌────────┼─────────┐
        │        │         │
     Status    Headers    Body
      Code
```
---

19. Commands Learned

Check process

ps -ef | grep <application-name>

Check listening TCP sockets

ss -lntp

Filter a specific port

ss -lntp | grep :8080

Test HTTP

curl http://localhost:8080/orders

Verbose HTTP troubleshooting

curl -v http://localhost:8080/orders

Send POST request

curl -X POST http://localhost:8080/orders \
  -H "Content-Type: application/json" \
  -d '{"product":"Laptop","quantity":1}'

---

20. Day-1 Revision Questions

Don't look at the notes while answering these.

Foundation

1. What does "ps" prove?
2. What is a process?
3. What is a port?
4. What is a socket?
5. What does "ss -lntp" prove?
6. What is TCP?
7. How is a TCP listening socket created?
8. What is the difference between "127.0.0.1" and "0.0.0.0"?

HTTP

9. What is HTTP?
10. What does "curl" actually do?
11. What are the parts of an HTTP request?
12. What is an HTTP method?
13. Difference between GET and POST?
14. What is a URL?
15. What is a path?
16. What is a query parameter?
17. What is an HTTP header?
18. Are headers present in requests, responses, or both?
19. Difference between "Content-Type" and "Accept"?
20. What is an HTTP body?
21. What are the parts of an HTTP response?
22. What does HTTP 200 mean?
23. Difference between 401 and 403?
24. What does 404 mean?
25. What does 500 mean?
26. What does 502 mean?
27. What does 504 mean?

Troubleshooting

28. Developer says "Orders API is down." What do you check first?
29. Process is running but port 8080 isn't listening. What does that tell you?
30. Port is listening but curl returns 404. What does that tell you?
31. Curl returns 401. Which area do you investigate?
32. Curl returns 403. Which area do you investigate?
33. Curl returns 500. What would you investigate next?

---

Golden Mental Model

«Don't troubleshoot by guessing. Prove each layer.»

Process
   ↓
Socket / Port
   ↓
TCP
   ↓
HTTP Request
   ↓
Application
   ↓
HTTP Response
   ↓
Identify the failing layer
   ↓
Investigate only what the evidence supports

This is the foundation for the deeper troubleshooting roadmap.
