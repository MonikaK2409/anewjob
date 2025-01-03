pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Creating virtual environment and installing dependencies...'
                script {
                    // Create a virtual environment
                    sh 'python3 -m venv ${WORKSPACE}/.venv'
                    // Install dependencies from requirements.txt inside the virtual environment
                    sh '''
                    . ${WORKSPACE}/.venv/bin/activate
                    pip3 install -r ${WORKSPACE}/requirements.txt
                    '''
                }
            }
        }
        stage('Test') {
            steps {
                echo 'Running tests...'
                script {
                    // Run tests using the virtual environment
                    sh '''
                    . ${WORKSPACE}/.venv/bin/activate
                    python3 -m unittest discover -s .
                    '''
                }
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                script {
                    // Ensure virtual environment is active during deployment
                    sh '''
                    . ${WORKSPACE}/.venv/bin/activate
                    mkdir -p ${WORKSPACE}/python-app-deploy
                    cp ${WORKSPACE}/app.py ${WORKSPACE}/python-app-deploy/
                    '''
                }
            }
        }
        stage('Run Application') {
            steps {
                echo 'Running application...'
                script {
                    // Run the application in the background, using the virtual environment
                    sh '''
                    . ${WORKSPACE}/.venv/bin/activate
                    nohup python3 ${WORKSPACE}/python-app-deploy/app.py > ${WORKSPACE}/python-app-deploy/app.log 2>&1 &
                    echo $! > ${WORKSPACE}/python-app-deploy/app.pid
                    '''
                }
            }
        }
        stage('Test Application') {
            steps {
                echo 'Testing application...'
                script {
                    // Run the test script using the virtual environment
                    sh '''
                    . ${WORKSPACE}/.venv/bin/activate
                    python3 ${WORKSPACE}/test_app.py
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for more details.'
        }
    }
}
