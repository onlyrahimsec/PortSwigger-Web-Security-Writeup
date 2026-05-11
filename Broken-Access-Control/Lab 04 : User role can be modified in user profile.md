# Lab 04: User role can be modified in user profile

**Category:** Broken Access Control 

**Level:** Apprentice (PortSwigger Academy)

## The Challenge
In this lab, the admin panel at `/admin` is restricted to users with a `roleid` of `2`. As a normal user, my `roleid` is `1`. The goal was to find a way to change my own role ID to gain administrative access and delete the user `carlos`.

## How I Solved It

### 1. Analyzing the Profile Update Feature
I logged in as `wiener` and noticed a feature to update my email address. I intercepted the "Update Email" request in **Burp Suite** and sent it to the **Repeater**. 

When I looked at the server's response, I saw that it was returning my user details in JSON format, specifically showing `"roleid": 1`.

### 2. Testing for Mass Assignment
Since the server was displaying the `roleid` in the response, I suspected it might also accept it as an input in the request (a "Mass Assignment" bug). 

I modified the JSON body in my POST request by adding `"roleid": 2` underneath my email address.

### 3. Verification & Exploitation
The server processed the request and replied with a `200 OK`, confirming that my `roleid` was now `2`. I refreshed my account page, found the "Admin panel" link, and deleted `carlos`.

## Evidence

**Step 1: Capturing the request in Burp Repeater** I sent the email update request to the Repeater to analyze the JSON structure.  
![Send to Repeater](../Image/send%20to%20repeater.png)

**Step 2: Modifying the Role ID parameter** I successfully injected the `"roleid": 2` parameter, which the server accepted and updated in the database.  
![Changing Role ID](../Image/able%20to%20chnage%20the%20roleid.png)

**Step 3: Gaining Admin Access** With my role updated, the administrative link became visible and accessible.  
![Entering Admin Panel](../Image/enter%20the%20admin%20panel.png)

**Step 4: Deleting Carlos & Lab Completion** I used the new privileges to delete the user and solve the lab.  
![Lab Solved](../Image/delete%20the%20user%20and%20lab%20solved.png)

## Why This Happens (And How to Fix It)
This is a **Mass Assignment** vulnerability. It happens when an application's framework takes a user-provided JSON object and maps it directly to a database model without filtering out sensitive fields.

**The Fix:**
Developers should use **DTOs (Data Transfer Objects)** or **allow-lists** to ensure that only specific, safe fields (like `email`) can be updated by the user. Sensitive fields like `roleid` should be strictly managed by the server and never accepted from a user's request.
