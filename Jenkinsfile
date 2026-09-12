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
        '''
      }
    }
    stage('Publish to Artifactory') {
        environment {
        ARTIFACTORY_CRED = credentials('Jfrog_Artifactory')
    }
          steps {
           sh '''
            while read -r line
            do
                source="${line#source=}"

                if grep -Fxq "$source" file.txt
                 then
                    zip -r "${source}.zip" "./build/${source}"

                    curl -X PUT \
                        -u "$ARTIFACTORY_CRED_USR:$ARTIFACTORY_CRED_PSW" \
                        --upload-file "${source}.zip" \
                        "http://host.docker.internal:8082/artifactory/DevOps/${JOB_NAME}/${BUILD_NUMBER}/${source}.zip"
                fi
            done < artifactory.properties
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
