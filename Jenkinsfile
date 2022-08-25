pipeline {
    agent any
    stages {
        stage('Test') {
            steps {
                sh 'mvn clean test'
            }
        }
        stage('Build') {
            steps {
                sh '''
                ssh -i ./.ssh/id_rsa jenkins@35.178.16.249 << EOF
                git clone https://github.com/PCMBarber/Spring-ToDo.git
                cd Spring-ToDo
                git checkout development
                git pull
                mvn clean install
                mkdir -p /home/jenkins/project-wars
                mv ./target/*.war /home/jenkins/project-wars/project-${BUILD_NUMBER}.war
                EOF
                '''
            }
        }
        stage('Deploy') {
            steps {
                sh '''
                ssh -i ./.ssh/id_rsa jenkins@35.178.16.249 << EOF
                build_num=${BUILD_NUMBER}
                echo '[Unit]
Description=My SpringBoot App

[Service]
User=jenkins
Type=simple

ExecStart=/usr/bin/java -jar /home/jenkins/project-wars/project-'$build_num'.war

[Install]
WantedBy=multi-user.target' > /home/jenkins/MyApp.service
                sudo mv /home/jenkins/MyApp.service /etc/systemd/system/MyApp.service
                sudo systemctl daemon-reload
                sudo systemctl restart MyApp
                EOF
                '''
            }
        }
    }
}
