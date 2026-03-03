## Basic SSRF against the local server

Lets say this is the request to server

POST /product/stock HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 118

stockApi=http://stock.weliketoshop.net:8080/product/stock/check%3FproductId%3D6%26storeId%3D1

and replaced stockApi by stockApi=http://localhost/admin

If the request to the /admin URL comes from the local machine, the normal access controls are bypassed. The application grants full access to the administrative functionality, because the request appears to originate from a trusted location.

Sometimes you will require to provide that specific IP address like stockApi=http://192.168.0.68/admin
You can use intruder if you know range

---

## SSRF with blacklist-based input filters

Some applications block input containing hostnames like 127.0.0.1 and localhost, or sensitive URLs like /admin.

-Use an alternative IP representation of 127.0.0.1, such as 2130706433, 017700000001, or 127.1.
- You can use spoofed.burpcollaborator.net for this purpose.
-Obfuscate blocked strings using URL encoding or case variation. 
(like to access admin page you can replace "a" by double-URL encoding it to %2561 )
-switching from an http: to https:

---

## SSRF with whitelist-based input filters
Some applications only allow inputs that match, a whitelist of permitted values. The filter may look for a match at the beginning of the input, or contained within in it. You may be able to bypass this filter by exploiting inconsistencies in URL parsing.

The URL specification contains a number of features that are likely to be overlooked when URLs implement ad-hoc parsing and validation using this method:

You can embed credentials in a URL before the hostname, using the @ character. For example:

https://expected-host:fakepassword@evil-host
You can use the # character to indicate a URL fragment. For example:

https://evil-host#expected-host
You can leverage the DNS naming hierarchy to place required input into a fully-qualified DNS name that you control. For example:

https://expected-host.evil-host
You can URL-encode characters to confuse the URL-parsing code. This is particularly useful if the code that implements the filter handles URL-encoded characters differently than the code that performs the back-end HTTP request. You can also try double-encoding characters; some servers recursively URL-decode the input they receive, which can lead to further discrepancies.
You can use combinations of these techniques together.

---

## Bypassing SSRF filters via open redirection

stockApi=http://weliketoshop.net/product/nextProduct?currentProductId=6&path=http://192.168.0.68/admin


## Blind SSRF with out-of-band detection

Use Burp Collaborator and replace url if present in json body or referer header
