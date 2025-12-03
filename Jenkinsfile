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
            // Define WAR file path
            def WAR_FILE = "${env.WORKSPACE}/target/news-app.war"

            // Current timestamp
            def currentDate = new java.text.SimpleDateFormat("yyyy-MM-dd_HH-mm").format(new Date())

            // Path inside Artifactory
            def targetPath = "feature_release1/${currentDate}/"

            // New Artifactory syntax
            def server = Artifactory.server('Jfrog')
            def buildInfo = Artifactory.newBuildInfo()

            server.upload(
                spec: """{
                    "files": [
                        {
                            "pattern": "${WAR_FILE}",
                            "target": "${targetPath}"
                        }
                    ]
                }""",
                buildInfo: buildInfo
            )

            // Publish build info (optional)
            server.publishBuildInfo(buildInfo)
        }
    }
}
