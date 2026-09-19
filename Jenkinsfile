pipeline {
    agent any

    options {
        timestamps()
        timeout(time: 10, unit: 'MINUTES')
    }

    triggers {
        // Revisa el repositorio cada 2 minutos (opcional si no usas webhooks)
        pollSCM('H/2 * * * *')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Instalar dependencias') {
            steps {
                sh '''
                    python3 -m venv .venv
                    . .venv/bin/activate
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Análisis de código (lint)') {
            steps {
                sh '''
                    . .venv/bin/activate
                    flake8 app tests
                '''
            }
        }

        stage('Pruebas unitarias') {
            steps {
                sh '''
                    . .venv/bin/activate
                    pytest --junitxml=reports/resultados.xml --cov=app --cov-report=xml:reports/coverage.xml
                '''
            }
            post {
                always {
                    junit 'reports/resultados.xml'
                }
            }
        }

        stage('Empaquetar') {
            steps {
                sh 'tar -czf calculadora-${BUILD_NUMBER}.tar.gz app'
                archiveArtifacts artifacts: '*.tar.gz', fingerprint: true
            }
        }
    }

    post {
        success { echo 'Pipeline exitoso: código validado y empaquetado.' }
        failure { echo 'Pipeline fallido: revisar la etapa marcada en rojo.' }
        cleanup { cleanWs() }
    }
}
