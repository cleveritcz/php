// Uses Declarative syntax to run commands inside a container.
pipeline {
    agent {
        kubernetes {
            yaml '''
kind: Pod
metadata:
  name: kaniko
  namespace: jenkins
spec:
  containers:
  - name: shell
    image: gcr.io/kaniko-project/executor:debug
    imagePullPolicy: IfNotPresent
    env:
     - name: container
       value: "docker"
    command:
     - /busybox/cat
    tty: true
    volumeMounts:
      - name: jenkins-docker-cfg
        mountPath: /kaniko/.docker
  volumes:
    - name: jenkins-docker-cfg
      projected:
        sources:
        - secret:
            name: regcred
            items:
              - key: .dockerconfigjson
                path: config.json
'''
            defaultContainer 'shell'
        }
    }
  stages {
    stage('Build') {    
      steps {
        container(name: 'shell') {
          sh '/kaniko/executor --dockerfile `pwd`/Dockerfile --context `pwd` --destination=cleveritcz/php:$PHP_VERSION --build-arg PHP_VERSION=$PHP_VERSION'
        }

      }
    }

  }
  environment {
    PHP_VERSION = '7.4'
  }
}
