pipeline {
  /* ---------- Global settings ---------- */
  agent any                         // run on any free agent/executor
  tools {                           // auto-install names set in “Global Tool Configuration”
    maven 'Maven 3'
  }

  environment {
    // optional: where your inventory & playbook live inside the repo
    INVENTORY = 'ci/hosts.ini'
    PLAYBOOK  = 'ci/deploy.yml'
  }

  /* ---------- CI/CD flow ---------- */
  stages {

    stage('Checkout') {
      steps {
        checkout scm                // pulls the branch that triggered the build
      }
    }

    stage('Build & Test (Maven)') {
      steps {
        sh 'mvn clean test package'
      }
      post {
        always {
          junit '**/target/surefire-reports/*.xml'      // show test results
          archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        }
      }
    }

    stage('Deploy with Ansible') {
      steps {
        // If Ansible isn’t in Jenkins PATH, give full path: /usr/bin/ansible-playbook
        sh "ansible-playbook -i ${INVENTORY} ${PLAYBOOK}"
      }
    }
  }

  /* ---------- Optional notifications, clean-up, etc. ---------- */
  post {
    success { echo "Build & deploy succeeded" }
    failure { echo "Something went wrong; check console log" }
  }
}
