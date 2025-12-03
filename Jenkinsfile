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

                    def warFile = "${env.WORKSPACE}/target/news-app.war"
                    def currentDate = new java.text.SimpleDateFormat("yyyy-MM-dd_HH-mm").format(new Date())
                    def targetPath = "feature_release1/${currentDate}/"

                    // Connect to the JFrog instance registered in Jenkins
                    def server = Artifactory.server('Jfrog')

                    // Upload Spec
                    def uploadSpec = """{
                        "files": [
                            {
                                "pattern": "${warFile}",
                                "target": "${targetPath}"
                            }
                        ]
                    }"""

                    // Upload the file
                    server.upload(uploadSpec)

                    echo "Artifact uploaded to JFrog at path: ${targetPath}"
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                sh """
                    echo 'Cleaning old deployment'
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
