pipeline {
  agent any
  tools {
    gradle "7.0"
  }
  environment {
    TESTPLAN = "${params.TESTPLAN}"
    TAG = "${params.TAG}"
  }
  stages {
    stage('CleanWorkspace') {
      steps {
        cleanWs()
      }
    }
    stage('Git') {
      steps {
        git branch: 'main', url: 'https://github.com/RichardChamorro/Jenkinsfile_pruebas_automatizadas.gi t'
      }
    }
    stage('Build') {
      steps {
        sh "gradle --version"
        sh "java --version"
        echo "$TESTPLAN"
        echo "$TAG"
        sh "pwd"
        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE')
        {
            sh 'gradle runWithCucumber -P tags="@$TEST_PX-1006"'
        }
      }
    }
    stage('Xray') {
      steps {
        echo "xray"
        sh '''
          token=$(curl -H "Content-Type: application/json" -X POST --data @"cloud_auth.json" https://xray.cloud.getxray.app/api/v2/authenticate| tr -d '"')
          curl -H "Content-Type: multipart/form-data" -X POST -F info=@Info.json -F results=@src/test/resources/output/report/Cucumber.json -H "Authorization: Bearer $token" https://xray.cloud.getxray.app/api/v2/import/execution/cucumber/multipart
          '''
      }
    }
  }
}
