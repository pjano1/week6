podTemplate(containers: [
    containerTemplate(
      name: 'gradle',
      image: 'gradle:6.3-jdk14',
      command: 'sleep',
      args: '30d'
      ),
    ]) {
      node(POD_LABEL) {
        stage('Run pipeline against a gradle project - test MAIN') {
          git branch: 'main', url: 'https://github.com/pjano1/week6.git'
          container('gradle') {
            stage('Build a gradle project') {
                echo "I am the ${env.BRANCH_NAME} branch"
            } 
            stage('Code coverage') {
                echo "My CC branch is: ${env.BRANCH_NAME}"
                if (env.BRANCH_NAME == "main") {
                   echo "I am the ${env.BRANCH_NAME} branch"
                   try {
                           sh '''
                           pwd
                           cd Chapter08/sample1
                           chmod +x gradlew
                           ./gradlew jacocoTestCoverageVerification
                           ./gradlew jacocoTestReport
                           '''
                   } catch (Exception E) {
                           echo 'Failure detected'
                   }

                   publishHTML (target: [
                     reportDir: 'Chapter08/sample1/build/reports/jacoco/test/html',
                     reportFiles: 'index.html',
                     reportName: "JaCoCo Report"
                   ])
                }
            }
          }
        }
      }
}