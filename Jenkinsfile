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
                    # Find the PID of the running server
                    OLD_PID=$(pgrep -f "server" || echo "")
                    
                    # Kill the process if it exists
                    if [ ! -z "$OLD_PID" ]; then
                        echo "Stopping server with PID $OLD_PID"
                        kill $OLD_PID
                        sleep 2
                        
                        # Force kill if still running
                        if ps -p $OLD_PID > /dev/null; then
                            echo "Force killing server with PID $OLD_PID"
                            kill -9 $OLD_PID
                        fi
                    else
                        echo "No running server found"
                    fi
                    
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
