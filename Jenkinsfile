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
        sh '''
             mkdir -p build
             while read -r file
             do
               cp --parents $file ./build/
               folder=$(find ./build/ -type d)
               zip -r ./build/$folder.zip ./build/$folder 
             done < file.txt
               
        '''
      }
    }
    stage('Publish to Artifactory') {
        environment {
        ARTIFACTORY_CRED = credentials('Jfrog_Artifactory')
    }
          steps {
           sh '''
                    ls -ltr ./build/*.zip
                    cp ./build/*.zip .
                    echo "Pusblishing to Artifactory"     
                    curl -X PUT \
                        -u "$ARTIFACTORY_CRED_USR:$ARTIFACTORY_CRED_PSW" \
                        --upload-file "*.zip" \
                        "http://host.docker.internal:8082/artifactory/DevOps/${JOB_NAME}/${BUILD_NUMBER}/"
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
