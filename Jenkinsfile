pipeline {
  agent any
  stages {
    stage('Git checkout') {
      steps {
        git(branch: 'master', url: 'https://github.com/PrabhuVignesh/movie-crud-flask.git')
        sh 'ls'
      }
    }

    stage('Python Flask Prepare') {
      steps {
        sh 'pip3 install -r requirements.txt'
      }
    }

    stage('Unit Test') {
      steps {
        sh 'python3 test_basic.py'
      }
    }

    stage('Python Bandit Security Scan') {
      steps {
        sh 'cat report/banditResult.json'
        sh 'sh run_bandit.sh || true'
        sh 'ls'
      }
    }

    stage('Dependency Check with Python Safety') {
      steps {
        sh 'docker run --rm --volume $(pwd) pyupio/safety:latest safety check'
        sh 'docker run --rm --volume $(pwd) pyupio/safety:latest safety check --json > report.json'
      }
    }

    stage('Static Analysis with python-taint') {
      steps {
        sh 'docker run --rm --volume $(pwd) vickyrajagopal/python-taint-docker pyt .'
      }
    }

    stage('sonar-publish') {
      steps {
        echo '===========Performing Sonar Scan============'
        sh "${tool("sonarqube")}/bin/sonar-scanner -Dsonar.login=143dc5c09e7c859e595f8d541a83da7f559c4667"
      }
    }

    stage('Dynamic Analysis - DAST with OWASP ZAP') {
      steps {
        sh 'docker run -t owasp/zap2docker-stable zap-baseline.py -t http://192.168.18.23:5000/ || true'
      }
    }

  }
}