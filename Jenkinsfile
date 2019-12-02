def FAILED_STAGE

pipeline {
    agent any

    tools {
        maven "Maven 3.6.3"
}

    stages {
        stage('Build') {
            steps {
                echo 'Running Build automation'
                script { FAILED_STAGE=env.STAGE_NAME }
                dir("project") {
                    sh "mvn clean test"
                }
            }
        }
        stage('Database') {
            steps {
                echo 'Running Database automation'
                script { FAILED_STAGE=env.STAGE_NAME }
                dir("database") {
                    sh "mvn clean test -Dscope=FlywayMigration"
                }
            }
        }
        stage('Deploy') {
            steps {
                echo 'Running Deploy automation'
                script { FAILED_STAGE=env.STAGE_NAME }
                dir("project") {
                    sh "mvn clean install"
                }                
            }
        }
        stage('Run Tests') {
            parallel {
                stage('Performance Test') {
                    steps {
                        echo 'Running Performance test'
                        script { FAILED_STAGE='Run Tests' }
                        dir("test") {
                            sh "mvn clean test -Dscope=performance"
                        }
                    }
        		}                    
                stage('Regression Test') {
                    steps {
                        echo 'Running Regression rest'
                        script { FAILED_STAGE='Run Tests' }
                        dir("test") {
                            sh "mvn clean test -Dscope=regression; exit 1"
                        }
                    }
        		}                    
                stage('Integration Test') {
                    steps {
                        echo 'Running Integration test'
                        script { FAILED_STAGE='Run Tests' }
                        dir("test") {
                            sh "mvn clean test -Dscope=integration"
                        }
                    }                                        
                }
            }

        }

 	}

        post {
            success {
               mail to: 'my@box.com',
                   subject: "SUCCESS: ${currentBuild.fullDisplayName}",
                   body: "The build ${env.BUILD_URL} has been completed"
               echo "The build ${env.BUILD_URL} has been completed"
            }
            failure {
               mail to: 'my@box.com',
                   subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
                   body: "The build ${env.BUILD_URL} has been failed. Something is wrong on ${FAILED_STAGE} stage"     
               echo "The build ${env.BUILD_URL} has been failed. Something is wrong on ${FAILED_STAGE} stage"           
            }
        }
}




