# Command Injection

Web Applications frequently execute commands into the operating system to function properly. When a developer builds an application that send user input directly into the Operating System without any verification, an attacker can use that to inject commands and take over the system.
This is called Command Injection. It is a Vulnerability that allows arbitrary OS_level commands to be executed.
The commands injected run as the same level as the web application. If the web application runs as www-data, the injected commands too.

A Command Injection is different fron a Remote Code Execution (RCE). A RCE describes the ability to execute code on a remote system and a Command Injection is one way of doing so.

This vulnerability exists because there are lots of programming languages that provide built-in functions that allows application code to execute commands in the OS. Some examples are PHP, Python and Node.js.

We can test if Command Injection is possible by observing how an application behaves. Applications that take user input and use it to build OS commands can be manipulated into executing bad operations.
Some Key tools are shell operators (; & &&) that allow the execution of multiple commands consecutively.

Command Injection can be detected in two ways : Verbose Command Injection and Blind Command Injection.

Verbose Command Injection send the output of the Injected command in its response allowing use to see the output.
Blind Command Injection still executes on the server but no output is shown from the web application. We need to rely on indirect signals.

## Detecting Blind Command Injection

The best way to detect blind Command Injection is to use payloads with a time delay such as ping or sleep.
If the response is longer than usual, it means that the payload is working.
Another method could be to force the output of the payload in a file using redirection operators (< > >>).
