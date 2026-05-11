# Lab 03: Bypassing Admin Security by Changing a Cookie

**Category:** Broken Access Control
**Level:** Apprentice (PortSwigger Academy)

## The Challenge
In this lab, the website has an admin panel at `/admin`, but it’s protected. Instead of checking a password on the server, the site looks at a cookie in my browser to see if I’m an admin. If the cookie says `false`, I’m a regular user. If it says `true`, the site thinks I have administrative rights.

## How I Solved It

### 1. Spotting the Vulnerability
I logged in with my normal user credentials (`wiener:peter`) and opened **Burp Suite** to see what was happening behind the scenes. 

While looking at the HTTP requests, I noticed a specific parameter in the `Cookie` header called `Admin=false`. This is a huge red flag because it suggests the server is letting the user's browser decide their permission level.

### 2. Manipulating the Request
I used Burp Proxy to intercept the request. I manually changed the value from `Admin=false` to `Admin=true` and forwarded it. This tricked the server into thinking my session had admin privileges.

### 3. Deleting the User
Once I had access to the admin panel, I clicked the delete button for `carlos`. I made sure to intercept this request as well to verify that the `Admin=true` cookie was still being sent so the server would allow the deletion.

## Evidence

**Step 1: Modifying the Cookie in Burp Suite** I changed the value from false to true to escalate my privileges.
![Admin Cookie Discovery](../Image/admin%20true.png)

**Step 2: Gaining Access to the Admin Panel** The server accepted the fake cookie and showed me the "Admin panel" link.
![Entering Admin Panel](../Image/admin%20panel%20enter.png)

**Step 3: Sending the Delete Request** This shows the actual HTTP request used to delete the user while using the forged cookie.
![Deleting Carlos](../Image/delete%20carlos.png)

**Step 4: Lab Successfully Solved** Confirmation that the user was deleted and the lab is finished.
![Lab Solved](../Image/lab%20done.png)

## Why This Happens (And How to Fix It)
This is a classic "Broken Access Control" issue. The developer trusted the user's browser to tell the truth about their role.

**The Fix:**
You should never use simple, forgeable cookies to decide who gets into the admin area. Instead, the server should check the user's session ID against a secure database on the **server-side** to see what their actual role is.
