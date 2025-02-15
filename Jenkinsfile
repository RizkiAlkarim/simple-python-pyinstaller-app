pipeline {
    agent none
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
        stage('Manual Approval'){
            steps {
                input message: 'Lanjutkan ke tahap Deploy?', ok: 'Proceed'
            }
        }
        stage('Deploy') {
            agent {
                docker {
                    image 'cdrx/pyinstaller-linux:python2'
                }
            }
            steps {
                sh 'docker cp sources/add2vals.py $(docker create cdrx/pyinstaller-linux:python2):/src/sources/add2vals.py'
                sh 'docker run cdrx/pyinstaller-linux:python2 ls -l /src/sources'
                sh 'docker run cdrx/pyinstaller-linux:python2 pyinstaller --onefile /src/sources/add2vals.py'
                sleep(time: 60, unit: 'SECONDS')
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals'
                }
            }
        } 
   }
}
