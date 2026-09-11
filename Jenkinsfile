pipeline {
  agent any
  stages {
    stage('Check Diff') {
      steps {
        echo "git diff --name-only origin/master...HEAD > file.txt"
        cat file.txt
      }
    }
  }
}
