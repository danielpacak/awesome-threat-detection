# Damn Vulnerable Web Application (DVWA)

## Overview

Damn Vulnerable Web Application (DVWA) is a PHP/MariaDB web application that is
damn vulnerable. Its main goal is to be an aid for security professionals to
test their skills and tools in a legal environment, help web developers better
understand the processes of securing web applications and to aid both students
and teachers to learn about web application security in a controlled class room
environment.

```
k apply -f code/dvwa/dvwa.kubernetes.yaml
```

```
k port-forward -n dvwa svc/dvwa-app 8888:8888
```

Navigate to <http://localhost:8888> and login as `admin:password`.

## Cleanup

```
k delete -f code/dvwa/dvwa.kubernetes.yaml
```

## Build a Custom Docker Container Image

```
git clone git@github.com:digininja/DVWA.git
cd DVWA
```

``` diff
-FROM docker.io/library/php:8-apache
+FROM docker.io/library/php:8.3-apache
```

```
docker buildx build -t ghcr.io/digininja/dvwa:8.3-apache .
```

```
docker image save -o /tmp/dvwa.tar ghcr.io/digininja/dvwa:8.3-apache
sudo ctr -n k8s.io image import /tmp/dvwa.tar && rm /tmp/dvwa.tar
```

## Further Reading

* <https://github.com/digininja/DVWA>
* <https://medium.com/@sonalipriyankasamantaray/penetration-testing-with-dvwa-damn-vulnerable-web-application-e61f5ac9cb24>
* <https://www.youtube.com/watch?v=WkyDxNJkgQ4>
* <https://owasp.org/www-project-vulnerable-web-applications-directory/>
