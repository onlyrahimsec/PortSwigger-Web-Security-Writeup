
# Lab: Unprotected admin functionality
**Category:** Broken Access Control

**Level:** Apprentice

##  Objective
The goal of this lab is to access the hidden administrative panel and delete the user `carlos`.

##  Methodology
1. **Reconnaissance:** I checked the `robots.txt` file by navigating to `/robots.txt`.
2. **Discovery:** The file revealed a disallowed path: `/administrator-panel`.
3. **Exploitation:** I navigated to that path and found a dashboard with user management privileges.
4. **Action:** I clicked "Delete" on the user `carlos`.

## Proof
![Admin Panel Success](../Image/Unprotected%20admin%20functionality.png)
