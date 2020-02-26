pipeline {
    agent none
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'python:2-alpine'
                }
            }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'qnib/pytest'
                }
            }
            steps {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
    stages {
        stage('Deliver') { 
            agent {
                docker {
                    //image 'cdrx/pyinstaller-linux:python2'
                    //args  "--user 0:0 --entrypoint=''"
                    args  "--user 0:0"
                    //image 'python:3.7-slim'
                    image 'debian:10-slim'
                }
            }
            steps {
                //sleep 4
                sh 'apt-get update -y && apt-get -y install python-pip'
                sh 'pip install pyinstaller'
                //sh '/entrypoint.sh'
                sh 'pyinstaller --onefile sources/add2vals.py' 
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals' 
                }
            }
        }
    }
}
