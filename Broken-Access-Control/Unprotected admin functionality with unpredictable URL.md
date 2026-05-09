# Lab: Unprotected admin functionality with unpredictable URL

**Category:** Broken Access Control
**Level:** Apprentice (PortSwigger Academy)

## The Challenge
This lab was a bit trickier than the last one. Instead of finding the admin path in a common file like `robots.txt`, the developers tried to hide it by using a "secret" URL. My job was to find that URL and delete the user `carlos`.

## How I Solved It

### 1. Digging into the Source Code
Since I couldn't see an "Admin" button anywhere on the homepage, I decided to look under the hood. I right-clicked on the page and selected **"View Page Source"** (or used Ctrl + U). 

I wasn't looking for fancy design; I was looking for links or scripts.

### 2. Finding the "Secret" Link
While scrolling through the code, I found a small piece of JavaScript. Inside that script, there was a variable that contained a random string of characters leading to an admin path. 

Even though the URL was "unpredictable" (like `/admin-qwer123`), the developers made the mistake of leaving the link right there in the public code for anyone to see.

### 3. Deleting the User
I copied that secret path, pasted it into my browser, and—boom—I was in the admin panel. Since the panel didn't ask me for a password or check if I was actually an admin, I just clicked the delete button next to `carlos` to solve the lab.

## Evidence

**Step 1: Spotting the admin link in the JavaScript source**
![Admin Location Discovery](../Image/find%20the%20admin%20location.png)

**Step 2: Successfully deleting the user**
![User Deleted](../Image/successfully%20delete%20the%20user.png)

## Why This Happens (And How to Fix It)
The mistake here is called **"Security through Obscurity."** Just because a URL is hard to guess doesn't mean it is secure. 

**The Fix:** 
The website should check on the server side if a user is actually an admin before showing them that page. You should never put sensitive links in your public JavaScript code where a curious user (or an attacker) can find them.
