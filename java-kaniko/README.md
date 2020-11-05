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


### Sample Jenkins Pipeline

```
pipeline {
    agent {label 'java-kaniko-slave'}

    stages {
        stage('Hello') {
            steps {
            
                git branch: 'main', url: 'https://github.com/mypractice96/spring-mvc-sample.git'
                sh 'mvn clean install'                
                sh 'executor --dockerfile=Dockerfile --context=$(pwd) --destination=mypractice96/springmvcsample:kaniko'
                
            }
        }
    }
}
```



