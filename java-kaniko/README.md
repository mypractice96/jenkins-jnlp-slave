## Java Kaniko JNLP Image


This image consists of
* git
* java 8
* maven
* kaniko
* sonar-scanner
* ssh


*Note : Use settings.xml for maven, if you are running behind proxy and uncomment **COPY settings.xml /root/.m2/** in Dockerfile*

*Note : Change **config.json** with your docker repository URL and credentials*

For credentials
```
echo -n 'repousername:password' | base64
> cmVwb3VzZXJuYW1lOnBhc3N3b3Jk
```



