# Python

## Using Web Libraries

### The urllib2 Library for Python 2.x

The urllib2 library is used in code written for Python 2.x. It's bundled into
the standard library. People use the urllib2 library when creating tools to
interact with web services.

``` python
import urllib2
url = 'https://danielpacak.github.io/awesome-threat-detection/'
response = urllib2.urlopen(url) # GET
print(response.read())
response.close()
```

The urllib2 library includes a Request class that gives you more fine-grained
control over how you make HTTP requests, including being able to define specific
headers, handle cookies, and create POST requests.

``` python
import urllib2
url = 'https://danielpacak.github.io/awesome-threat-detection/'
headers = {'User-Agent': 'Googlebot'}

request  = urllib2.Request(url,headers=headers)
response = urllib2.urlopen(request)

print(response.read())
response.close()
```

### The urllib Library for Python 3.x

In Python 3.x, the standard library provides the `urllib` package, which splits
the capabilities from the `urllib2` package into the `urllib.request` and
`urllib.error` subpackages. It also adds URL-parsing capability with the
subpackage `urllib.parse`.

``` python
import urllib.parse
import urllib.request

url = 'https://danielpacak.github.io/awesome-threat-detection/'
with urllib.request.urlopen(url) as response:  # GET
    content = response.read()

print(content)
```
