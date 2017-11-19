 using hostname.

[code language="java"]
node {

    withMaven(maven:'maven') {

        stage('Checkout') {
            git url: 'https://github.com/dblitz/microservices-emr.git', credentialsId: 'dblitz', branch: 'master'
        }

        stage('Build') {
            sh 'mvn clean install'

            def pom = readMavenPom file:'pom.xml'
            print pom.version
            env.version = pom.version
        }

        stage('Image') {
            dir ('patient-service') {
                def app = docker.build "localhost:5000/patient-service:${env.version}"
                app.push()
            }
        }

        stage ('Run') {
            docker.image("localhost:5000/patient-service:${env.version}").run('-p 2222:2222 -h patient --name patient --link discovery')
        }

        stage ('Final') {
            build job: 'appointment-service-pipeline', wait: false
        }      

    }

}