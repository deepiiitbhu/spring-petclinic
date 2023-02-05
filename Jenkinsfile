pipeline {
  agent {
    node {
      label 'test'
    }

  }
  stages {
    stage('Compile') {
      steps {
        sh './mvnw clean compile'
      }
    }

    stage('Static Analysis') {
      steps {
        sh '''./mvnw sonar:sonar \\
  -Dsonar.host.url=http://172.31.25.253:9000/ \\
  -Dsonar.projectKey=PetClinic \\
  -Dsonar.login=sqp_fc74c8773c9f8e135bc81a904d3b423ca30bd6ec'''
      }
    }

    stage('Unit Test') {
      steps {
        sh './mvnw "-Dtest=**/petclinic/*/*.java" test'
      }
    }

    stage('Package') {
      steps {
        sh './mvnw package -DskipTests=true'
      }
    }

    stage('Deploy') {
      parallel {
        stage('Deploy') {
          steps {
            sh './mvnw spring-boot:run </dev/null &>/dev/null &'
          }
        }

        stage('Integration and Performance Tests.') {
          steps {
            sh './mvnw verify'
            perfReport '**/target/jmeter/results/*'
          }
        }

      }
    }

  }
}