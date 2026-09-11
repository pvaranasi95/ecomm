pipeline {
  agent any
  stages {
    stage('Check Diff') {
      steps {
        sh "git fetch origin master"
         sh "git diff --name-only FETCH_HEAD...HEAD > file.txt"
        sh "cat file.txt"
      }
    }
    stage('Package') {
      steps {
        sh "mkdir -p build"
        sh '''
             while read -r file
             do
               cp $file ./build
             done < file.txt
            ls -ltr ./build 
            zip -r build.zip ./build
        '''
      }
    }
    stage('Publish to Artifactory') {
        environment {
        ARTIFACTORY_CRED = credentials('Jfrog_Artifactory')
    }
      steps {
        sh '''
          curl -X PUT \
         -u "$ARTIFACTORY_CRED_USR:$ARTIFACTORY_CRED_PSW" \
         --upload-file "./build.zip" \
         "http://host.docker.internal:8082/artifactory/DevOps/${JOB_NAME}/${BUILD_NUMBER}/build.zip"
     '''
      }
    }
    stage('clean ws') {
      steps {
        cleanWs()
      }
    }
               
  }
}
