# Lab 05: Bypassing Front-End Security with X-Original-URL

**Category:** Broken Access Control / Bypass

**Level:** Practitioner (PortSwigger Academy)

## The Challenge
This lab features a common real-world setup: a **front-end system** (like a WAF or Reverse Proxy) and a **back-end application**. The front-end is configured to block any requests to `/admin`. However, the back-end is built on a framework that supports the `X-Original-URL` header, allowing an attacker to "hide" the actual requested path from the front-end filter.

## How I Solved It

### 1. Confirming the Block
I first tried to access `/admin` directly. I received a plain "Access Denied" message. This response looked like it came from a security filter (front-end) rather than the main application.

### 2. Testing for Header Support
I sent the request to **Burp Repeater**. I changed the main request line to `GET /` so the front-end would let it pass. Then, I added the header `X-Original-URL: /invalid`. 

The server responded with a **404 Not Found**, confirming that the back-end was ignoring the main URL and instead looking at my header to determine which page to serve.

### 3. Circumventing the Filter
I changed the header to `X-Original-URL: /admin`. 
* The **front-end** saw a request for `/` (Allowed).
* The **back-end** saw a request for `/admin` (Executed).
This successfully rendered the admin panel in the response.

### 4. Executing the Deletion
To delete the user `carlos`, I had to craft a specific request:
* Main URL: `/?username=carlos`
* Header: `X-Original-URL: /admin/delete`

This command bypassed the filter again and instructed the back-end to run the deletion script.

## Evidence

**Step 1: Testing Header Support** I used an invalid path to see if the server would respond with a 404, proving it reads the `X-Original-URL` header.
![Testing Header Support](../Image/add%20the%20HTTP%20header.png)

**Step 2: Accessing the Restricted Panel** By spoofing the path in the header, I bypassed the front-end block and reached the admin dashboard.
![Bypassing the Filter](../Image/chnage%20the%20HTTP%20header.png)

**Step 3: Successful Dashboard Access** The result in the browser showing the administrative interface.
![Admin Panel Access](../Image/able%20to%20access%20the%20admin%20panel.png)

**Step 4: Deleting the Target User** Capturing the final request that triggered the deletion of `carlos`.
![Delete Request](../Image/delete%20the%20user.png)

**Step 5: Lab Solved** Confirmation of the successful exploit.
![Lab Solved](../Image/lab%20solved.png)

## Why This Happens (And How to Fix It)
This is a **discrepancy** between how different parts of a web stack interpret a request. The front-end thinks it’s safe because it only checks the primary URL, while the back-end trusts a non-standard header.

**The Fix:**
1.  **Uniformity:** Both the front-end and back-end should be configured to handle headers and URLs in the exact same way.
2.  **Disable Overrides:** If the `X-Original-URL` or `X-Rewrite-URL` headers are not required for business logic, they should be disabled at the application framework level.
3.  **Positive Security:** Security filters should rely on "Allow-lists" for URLs rather than just blocking specific paths like `/admin`.
