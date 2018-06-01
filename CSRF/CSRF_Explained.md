Cross site request forgery

it's a vulnerable that impact the web-browser and the websites configuration. this potentially have been found since beginning of 2000.

description:
the vulnerable in the web-browsers architecture, how it handles the authorization. but also in a websites code; how it allows to post based on the origin of a domain.

CSRF its a technic that let you pass data from one domain to another without any authorization from the user. the authorization its inside the web-browsers like the "session", "cookies".


#if we create a easy HTML-document with following code


GET
[code]<img src="http://i.imgur.com/123.jpg"/>[/code]

if we test the Web-browser, we will see the picture because we have got privileges, a request could look like below:

GET /123.jpg HTTP/1.1
Cookie:Authenticated

What happens if the same document points on a URL that makes something else where the user is logged-on a webpage that have valid Cookies.

#HTML-CODE
<img src="http://site.com/buy.php?to=hacker@hx.se&value=700$" />

Above code makes the same thing, but the tag doesn't shows a picture though, but it tries to visit a URL that actually is working though there is no picture shown.

#CODE
GET /buy.php?to=hacker@hx.se&value=700$ HTTP/1.1 
Cookie: authenticated  

GET is interesting because it the web-browser need to get a URL.
in this example you see Post-fields with values "to" and "value" are the variables that needs to be filled in, if we already create an URL with the parameters we need then it's enough that one user is already logged on to do the purchase make through.


#POST
the opposite to "GET" is "POST", when you send data. You still visit an URL with post-fields though the post fields doesn't exists in the URL.

GET /buy.php HTTP/1.1 
POST-DATA: to=hacker@hx.se&value=700$ 
Cookie: authenticated  

Above is how the should do the purchase with the POST-method(the post-data happens in the background) you only appear that we visited "buy.php" but our variables and values was in the post-method.


Detection:
"POST" and "GET", can do the same thing so the server understands.
to detect a potentially a GET-Request that is vulnerable against CSRF it's easy..

You only need to look in the "URL", if you appear variables that actually contains values then you found CSRF.


Imagine facebook have a GET vulnerable:
www.facebook.se/message.php?to=234627834834&message=push eax!!&do=send

"to", "message" and "do" is everything you need to be able to send the message.
All you need it's to send a GET-request to the URL then you activate the method/function "push eax!!" to user "234627834834" and the variable "do" it's actually "send".


to send an "Exploit", then you create an easy HTML-row that contains the URL, t


"to", "message" and "do" are all we need to send a message. 
So all you have to do is send a GET request to this URL so you will send push eax !! to the user "234627834834", the variable is sent. As you can see, it is very easy to detect a vulnerable GET.

To write an exploit to this, you simply create an HTML line containing this URL. Should a user be logged in to facebook and request a request for this URL, the message will be sent.

The easiest thing is clear to put this HTML code in an email and as soon as the user opens the mail, GET request is sent to facebook and sends the message.


To check your POST data, I recommend a plug-in to Firefox with the name Live HTTP Header, which allows you to view the data you send and resend it.

Let's say we have a vulnerable POST:
http://s18.postimg.org/xacss1kax/face.png

As you can see in the picture, exactly the same variables are as in the above GET request, but this time we find the data in the POST field. Should I now press the "Replay" button, this message will be sent and this is because no tokens are used, but I'll explain it later.

Creating an exploit to send POST data is almost as simple, but we need Javascript.

#HTML-CODE:
<form name="csrf" ENCTYPE="text/plain"
action="www.facebook.se/message.php" method="POST">
<input type="hidden" name='to=234627834834&message=push eax!!&do=send'>
</FORM>
<script>document.csrf.submit();</script>

Should you save the above HTML code in one documented and open in a browser, then two = 234627834834 & message = push eax !! & do = send to be sent to www.facebook.se/message.php.

Arrangement:

Fixing CSRF is simple, but something most people do not cope with. Generally, it is better to use POST instead of GET, but would need GET so it works to use sessions which will prevent CSRF

When it comes to GET, you need to enter a valid session. Without this session, you simply get an error and will not reach the page.

www.facebook.se/message.php?&to=234627834834&session=8237492730428340237420348273042834ng&message=push eax!!&do=send

POST can be a little different. You can first and foremost put SAMEORIGIN in the headline, which means that you are not allowed to send a request based on the origin of the domain, eg from page.se to facebook.se.

Flashback has the method of generating a unique snippet (Session Token) that is added to the POST data when sending the data. This plug is checked if it is the same in the source code as in the POST data. I've just mentioned this here.

But as you know, no responsibility lies with the website's encoder, but also the user. RequestPolicy, NoScript, and CsFire are examples of plug-ins that prevent you from being killed by CSRF bugs.

Not using "Keep me logged in" can stop attacks when authorization is required for a CSRF attack.
