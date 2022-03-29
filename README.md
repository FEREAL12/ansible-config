#### Jenkins File for Quick Task
```
pipeline {
    agent any

  stages {
    stage ('Initial cleanup') {
	steps {
	  dir("${WORKSPACE}"){
		deleteDir()
   }
  }
}
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }

    stage('Test') {
      steps {
        script {
          sh 'echo "Testing Stage"'
        }
      }
    }

   stage('package') {
	steps {
	  script {
	     sh 'echo "Packaging App"'
   }
 }
}

  stage('deploy') {
	steps {
	 script {
	   sh 'echo "deploying to dev"'
  }
 }
}
   stage ('cleanup') {
	steps {
	   cleanWs()
 }
}
    }
}
```
