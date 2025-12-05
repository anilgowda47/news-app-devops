pipeline {
    agent { label 'dev' }

    stages {

        stage('Checkout') {
            steps {
                sh "rm -rf news-app-devops"
                sh "git clone 'https://github.com/anilgowda47/news-app-devops.git'"
            }
        }

        stage('Version & Build') {
            steps {
                script {
                    def version = "1.0.${env.BUILD_NUMBER}"
                    echo "Setting project version to ${version}"

                    sh """
                        cd ${env.WORKSPACE}
                        mvn versions:set -DnewVersion=${version}
                        mvn clean package
                    """
                }
            }
        }

        stage('Test') {
            steps {
                sh "cd ${env.WORKSPACE} && mvn test"
            }
        }

        stage('Push the artifacts into JFrog Artifactory') {
            steps {
                script {
                    echo "JFrog Artifactory upload skipped - Dummy stage for testing only."
                    echo "WAR file would have been: ${env.WORKSPACE}/target/news-app.war"
                    echo "Target path would have been: feature_release1/<timestamp>/"
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                sh """
                    echo 'Cleaning the old deployment'
                    sudo rm -rf /opt/tomcat10/webapps/news-app /opt/tomcat10/webapps/news-app*.war

                    echo 'Copying new WAR'
                    sudo cp ${env.WORKSPACE}/target/news-app.war /opt/tomcat10/webapps/

                    echo 'Restarting Tomcat'
                    sudo /opt/tomcat10/bin/shutdown.sh || true
                    sleep 2
                    sudo /opt/tomcat10/bin/startup.sh
                """
            }
        }

    }
}
