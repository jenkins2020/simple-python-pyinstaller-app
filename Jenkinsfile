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
        stage('Deliver') {
            agent {
                docker {
                    /* args '--entrypoint=""' */
                    /* image 'cdrx/pyinstaller-linux:python2' */
                    args '-u 0:0'
                    image 'debian:stable'
                }
            }
            steps {
                /* sh 'sudo apt-get -y install pyinstaller python2'  */
                sh 'apt-get -y update && apt-get -y install python-setuptools python-pip python2' 
                sh 'pip install pyinstaller' 
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
