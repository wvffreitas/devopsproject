def username = 'Jenkins'
env.CC = 'clang'
node {
stage('Build') {
env.DEBUG_FLAGS = '-g'
echo 'Building..'
echo "Hello Mr. ${username}"
echo "Running ${env.JOB_NAME} (${env.BUILD_ID}) at ${env.JENKINS_URL}"  
deleteDir()
checkout scm
sh "echo 'res' > result"
stash includes: '**/result', name: 'app'
if (env.BUILD_ID.toInteger() % 2 == 0) {
   echo 'Execucao PAR'
    } else {
      echo 'Execucao IMPAR'
      } 
/*try {
      sh "exit ${exitCode}"
      echo 'Sucesso!'
      }
catch (e) {
      echo 'Falhou.'
        // throw e
      }
finally {
      echo 'Executa sempre.'
      }*/
sh 'cat README.md'
sh 'printenv'
          def ambiente = input id: 'test', message: 'Please Provide Parameters', ok: 'Next',
           parameters: [
              choice(name: 'ENVIRONMENT',
                  choices: ['dev','qa'].join('\n'),
                  description: 'Please select the Environment'),
              string(name: 'EXIT',
                  defaultValue: '0',
                  description: 'Please enter the exit code.')
           ]
exitCode = ambiente['EXIT']
   echo "${ambiente}"
}
   stage('Test') {
      node() {
         echo 'Testing..'
         node {
               git url: 'https://github.com/joe_user/simple-maven-project-with-tests.git'
               def mvnHome = tool 'M3'
               sh "${mvnHome}/bin/mvn -B -Dmaven.test.failure.ignore verify"
               archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
               junit **/target/surefire-reports/TEST-*.xml'
         }
/*retry(3) {
sh './flakey-deploy.sh'
}
timeout(time: 3, unit: 'MINUTES') {
sh './health-check.sh'
}*/
      parallel FrontendTests: { echo 'Testing Frontend..' },
               BackendTests: { echo 'Testing Backend..' }
      }
   }
stage('Deploy') {
   node() {
   echo 'Deploying....'
   deleteDir()
   unstash 'app'
   sh 'cat result'
   archiveArtifacts artifacts: '**/result', fingerprint: true
}
}
}
