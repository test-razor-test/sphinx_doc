pipeline {
    agent any
    options {
        timeout(time: 20, unit: 'MINUTES')
        buildDiscarder(logRotator(numToKeepStr: '25'))
    }

    environment {
        TIMESTAMP = "$BUILD_TIMESTAMP"               
    }

    stages {
        stage('Test and Debug Info') {
            steps {
                sh """
                    echo 'Show running user'
                    whoami                
                    echo 'Test and Setup'
                    echo 'Show Java version'
                    java --version
                    echo 'Show Docker version'
                    docker --version
                    echo 'Show PWD'
                    pwd
                    echo 'Show dir contents'
                    ls -la
                """                

            }
        }
        
        stage('Remove Existing Sphinx Containers') {
            steps {
                sh """
                    sudo docker container rm sphinx || true 
                    # the 'true' makes this pass every time
                    # So if the container isn't present and this command fails it doesn't break the build
                """
            }
        }
        
        stage('Sphinx Doc Setup') {
            steps {
                sh """
                    ls -la
                    rm /docs/* -R || true #true - won't fail if non-existent
                    mkdir /docs
                    chmod 777 /docs
                    cp ./* /docs/ -R
                    ls -la /docs/
                """
            }
        }
        
        stage('Run Sphinx') {
            steps {
                sh """
                    sudo docker run --rm -v /docs:/docs localhost:5000/sphinx-latexpdf:4.5.0 make html
                """
            }
        }      
    }
}
