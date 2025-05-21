pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                // Pull the latest code from your repository
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                // Compile your C server
                sh 'gcc server.c -o server'
            }
        }
        
        stage('Deploy') {
            steps {
                // Find the current process, kill it, and start a new one
                sh '''
                    pkill -f server 
                    
                    # Start the new server detached
                    echo "Starting new server instance"
                    nohup ./server > server.log 2>&1 &
                    
                    # Save the new PID for reference
                    echo $! > server.pid
                    echo "New server started with PID $!"
                '''
            }
        }
    }
    
    post {
        success {
            echo 'Server updated and restarted successfully!'
        }
        failure {
            echo 'Build or deployment failed!'
        }
    }
}
