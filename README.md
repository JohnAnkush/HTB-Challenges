# HTB-Challenges

Templated WEB Challenge of the webside Hack The Box writeup Capture The Flag

![Screenshot_2021-07-06_02-09-06](https://user-images.githubusercontent.com/57195844/124516931-6de4a780-de00-11eb-912e-0f14e836f6df.png)

We start the web instance and when we access we will see the following interface:

![Screenshot_2021-07-06_02-11-38](https://user-images.githubusercontent.com/57195844/124516979-81900e00-de00-11eb-93fe-d3523a0f0e0a.png)


I tried inspecting the item or using the network tab in dev tool, but found nothing.

So I will try to introduce routes in the url to see if something happens.

![Screenshot_2021-07-06_02-19-52](https://user-images.githubusercontent.com/57195844/124517086-b13f1600-de00-11eb-899c-eac34d2833f0.png)

I got a 404 error but notice what we entered as path in URL is getting rendered in the website. Let's try out a few payloads for XSS.


XSS Vulnerability

http://46.101.89.127:31382/<script>alert("hi")</script>

![Screenshot_2021-07-06_02-20-58](https://user-images.githubusercontent.com/57195844/124517168-f5321b00-de00-11eb-81bf-316e1f08b742.png)

I got nothing.. but trying another payload

<img src=x onerror="alert('xss')">

![Screenshot_2021-07-06_02-22-42](https://user-images.githubusercontent.com/57195844/124517230-16930700-de01-11eb-991c-0ad7b2b37b77.png)
![Screenshot_2021-07-06_02-23-49](https://user-images.githubusercontent.com/57195844/124517388-79849e00-de01-11eb-9b51-f2149987d32f.png)


Server Side Template Injection (SSTI)

Well, with this we know the web is vulnerable to XSS payloads, but this did not lead us anywhere to obtain the flag.

So thinking that the challenge is called "Templated" and that Jinja2 is a web template engine for the Python.

Maybe it's an SSTI (Server Side Template Injection) vulnerability.

Payload 1: http://46.101.89.127:31382/{{46+46}}

Result: it give 92 as output.

![Screenshot_2021-07-06_02-24-01](https://user-images.githubusercontent.com/57195844/124517403-7f7a7f00-de01-11eb-92cd-33a37ca5210f.png)

At the moment we found 2 vulnerabilities, SSTI and XSS (Reflected)

In python __mro__ or mro() allows us to go back up the tree of inherited objects in the current Python environment.

Saying that we can use the MRO function to display classes using the following payload:

{{"".__class__.__mro__[1].__subclasses__()[186].__init__.__globals__["__builtins__"]["__import__"]("os").popen("ls *").read()}}

![Screenshot_2021-07-06_02-27-03](https://user-images.githubusercontent.com/57195844/124517511-bfd9fd00-de01-11eb-9470-12a400ba13cb.png)

This lists all the files and guess what we can see flag.txt. All we need to do now is to replace ls * with cat flag.txt.

So the payload will be:

{{"".__class__.__mro__[1].__subclasses__()[186].__init__.__globals__["__builtins__"]["__import__"]("os").popen("cat flag.txt").read()}}

Finally we have obtained the flag

I have not given the the images of that, Please try it your self so that you can learn something new.
