# OWASP Juice Shop – TryHackMe Write-up

| | |
|---|---|
| **Difficulty** | Easy |
| **Category** | Injection - Broken Authentication - Sensitive data exposure - Broken Access Control - XSS |
| **Target OS** | Web application |
| **Date** | 15/11/2026 |

This CTF challenge is a bit particular because during the whole challenge, we are helped on how to do things and how to test things. This Writeup will explain task by task how to find the flags.

## Task 1

This Task just asks us to launch the attackbox and the target machine.

## Task 2

For this task, all we need to do is to look around the website with Burp. We need to explore the comments and the sections of the website to answer the questions asked.

## Task 3

For this task, we'll explore some Injection exploit. There are a lot of them such as SQLi, Command Injection or Email Injection (to send email messages without authorization bu the Email Server).
We will use SQLi. We first go to the login page to test it. We turn the intercept on on Burp. Now that the intercept is on, we can send a test request so that Burp can intercept it. We can then modify the email field to try our SQLi.
the email field becomes `"email":"' or 1=1--"`. We can send the request to get our first success flag and move on with the next question.

For the next question, we want to login with the bender account. To do that we start by making a test request intercepted by Burp and modify it to put our SQLi in it. To make it work we use those fields : 

`"email":"bender@juice-sh.op--"; "password":"test"`

It works because the `--` allows us to skip the password field completely and login to the bender account without using a password. We can then answer to the next question with the success flag we get from doing the task.

## Task 4

Within this task, we will exploit authentication vulnerabilities such as weak passwords in high privilege accounts, forgotten password pages and broken authentication.

For the first flag, we need to send a request to Burp intruder. We make a test request for bupr to intercept it to try to login with admin@juice-sh.op and the password. We want to bruteforce the password. In intruder we modify the password field to look like this:

`"password":"§§"`

We then add the payload located in `/usr/share/wordlists/SecLists/Passwords/Common-Credentials/best1050.txt` and run the exploit to bruteforce the password and login as the admin. With that we get the success flag.

For the Second flag, we use the Forgot password form to change the password using passive Osint on a star trek user (the response the the question is his brother's name).

## Task 5

We can see a hidden folder /ftp. For the first flag we can download the document `acquisitions.md` to get the first flag. For the second flag, we just need to enter the credentials learnt from the video with some social engineering.
The third flag is obtained in the same way as the first one but we just need to add a Poison Null Byte to bypass the security blocking us. We can only download .md or .pdf files so we add to the url this `%2500.md` to get the third flag.

## Task 6

 We need to open the debugger to find the document `main.js`. In this document, we find the wepage /administration, to access it we login with the admin account, go the the webpage and get the flag.
 For the second flag, we need to access the basket of another user. To do so we intercept the request with burp and modify the header to get the basket 2 instead of 1.
 For the Third flag, we need to go to the administration page and erase the 5 star comment.

## Task 7

For the first flag, We use a DOM XSS. The search bar is weak to XSS so we exploit it with this payload: `<iframe src="javascript:alert('xss')">`. We get the first flag.
For the second flag we intercept a request with burp and modify the header to set up a parameter manually. It allows us to ake a .js window appear. we get the second flag.
For the third flag we use the same payload and put it in the URL to exploit an IDOR.
