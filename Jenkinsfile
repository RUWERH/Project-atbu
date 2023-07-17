pipeline {
    agent any
    stages {
        stage("Build Stage") {
            steps {
                sh 'php --version'
                sh 'composer install'
                sh 'composer --version'
                sh 'cp .env.example .env'
                sh 'php artisan key:generate'
                sh 'cp .env .env.testing'
                
            }
        }
        stage("Unit test") {
            steps {
                sh 'php artisan test'
            }
        }
        stage("Code coverage") {
            steps {
                sh "vendor/bin/phpunit --coverage-html 'reports/coverage'"
            }
        }
        stage("Static code analysis larastan") {
            steps {
                sh "vendor/bin/phpstan analyse --memory-limit=2G"
            }
        }
        stage("Static code analysis phpcs") {
            steps {
                sh "vendor/bin/phpcs"
            }
        }
        stage("Docker build") {
            steps {
                sh "docker build -t ruwerh/atbu-project ."
            }
        }
        stage("Docker push") {
            steps {
                sh "docker login --username 'ruwerh' --password 'Codedx.100'
                sh "docker push ruwerh/atbu-project"
            }
        }
        stage("Deploy to staging") {
            steps {
                sh "docker run -d --rm -p 80:80 --name atbu-project ruwerh/atbu-project"
            }
        }
        stage("Acceptance test curl") {
            steps {
                sleep 20
                sh "chmod +x acceptance_test.sh && ./acceptance_test.sh"
            }
        }
        stage("Acceptance test codeception") {
            steps {
                sh "vendor/bin/codecept run"
            }
            post {
                always {
                    sh "docker stop atbu-project"
                }
            }
        }
    }
}
