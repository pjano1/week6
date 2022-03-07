podTemplate(containers: [
    containerTemplate(
      name: 'gradle',
      image: 'gradle:6.3-jdk14',
      command: 'sleep',
      args: '30d'
      ),
    ]) 
{
      node(POD_LABEL) {
        stage('Run pipeline against a gradle project - test MAIN') {
          git branch: 'main', url: 'https://github.com/pjano1/week6.git'
          container('gradle') {
            stage('Build a gradle project') {
                echo "I am the ${env.BRANCH_NAME} branch"
                sh '''
                chmod +x gradlew
                ./gradlew build
                mv ./build/libs/calculator-0.0.1-SNAPSHOT.jar /mnt
                '''
            } //gradle stage
            stage('Testing') {
                echo "My CC branch is: ${env.BRANCH_NAME}"
                if (env.BRANCH_NAME == "main") {
                   echo "I am the ${env.BRANCH_NAME} branch"
                   try {
                           sh '''
                           pwd
                           cd Chapter08/sample1
                           ./gradlew checkstyleMain
                           ./gradlew jacocoTestCoverageVerification
                           ./gradlew jacocoTestReport
                           '''
                   } catch (Exception E) {
                           echo 'Failure detected'
                   }
                } else if (env.BRANCH_NAME == "feature") {
                   echo "I am the ${env.BRANCH_NAME} branch"
                   try {
                           sh '''
                           pwd
                           cd Chapter08/sample1
                           ./gradlew checkstyleMain
                           ./gradlew jacocoTestReport
                           '''
                   } catch (Exception E) {
                           echo 'Failure detected'
                   }  
                } else if (env.BRANCH_NAME == "playground") {
                   echo "As ${env.BRANCH_NAME} branch, I am running NO tests!"
                }
                publishHTML (target: [
                  reportDir: 'Chapter08/sample1/build/reports/jacoco/test/html',
                  reportFiles: 'index.html',
                  reportName: "JaCoCo Report"
                ])
            } // Testing stage
                 
            stage('Build Java Image') { 
                container('kaniko') {
                  stage('Build a container') {
                      if (env.BRANCH_NAME != "playground") {
                         try {
                                 sh '''
                                 echo 'FROM openjdk:8-jre' >> Dockerfile
                                 echo 'COPY ./calculator-0.0.1-SNAPSHOT.jar app.jar' >> Dockerfile 
                                 echo 'ENTRYPOINT ["java", "-jar", "app.jar"]' >> Dockerfile
                                 mv /mnt/calculator-0.0.1-SNAPSHOT.jar . '''
                                 if (env.BRANCH_NAME == "main") {
                                    sh '''
                                    /kaniko/executor --context `pwd` --destination pjano1/calculator:1.0 '''
                                 } else if (env.BRANCH_NAME == "feature") {
                                   sh '''
                                   /kaniko/executor --context `pwd` --destination pjano1/calculator-feature:0.1 '''
                                 }
                         } catch (Exception E)  { 
                                 echo 'Failure detected' 
                         }
                      }
                  } // Build a container stage 
                } // container kaniko
            } // Build Java Image stage 
          } // container gradle 
        } // Run pipeline against a gradle project - test MAIN stage 
      } // POD_LABEL 
}