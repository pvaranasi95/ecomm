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
             done < file.txt
             folder=$(find ./build/ -type d -mindepth 1 | awk -F '/' {'print $3'})
               zip -r ./build/$folder.zip ./build/$folder
               
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
                    rm -rf ./build/
                    echo "Pusblishing to Artifactory"     
                    curl -X PUT \
                        -u "$ARTIFACTORY_CRED_USR:$ARTIFACTORY_CRED_PSW" \
                        --upload-file "${folder}.zip" \
                        "http://host.docker.internal:8082/artifactory/DevOps/${JOB_NAME}/${BUILD_NUMBER}/${folder}.zip"
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
