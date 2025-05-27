pipeline {
    agent any

    tools {
        nodejs 'Node_24' // Configurado en Global Tools
    }

    stages {
        // Etapa 1: Checkout
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/GustavoOspinaL/ucp-app-react.git'
            }
        }

        // Etapa 2: Build
        stage('Build') {
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }

        // Etapa 3: Pruebas Paralelizadas
        stage('Pruebas en Paralelo') {
            parallel {
                // Pruebas en Chrome
                stage('Pruebas Chrome') {
                    steps {
                        script {
                            try {
                                sh 'npm test -- --browser=chrome --watchAll=false --ci --reporters=jest-junit'
                                junit 'junit-chrome.xml'
                            } catch (err) {
                                echo "Pruebas en Chrome fallaron: ${err}"
                                currentBuild.result = 'UNSTABLE'
                            }
                        }
                    }
                }

                // Pruebas en Firefox
                stage('Pruebas Firefox') {
                    steps {
                        script {
                            try {
                                sh 'npm test -- --browser=firefox --watchAll=false --ci --reporters=jest-junit'
                                junit 'junit-firefox.xml'
                            } catch (err) {
                                echo "Pruebas en Firefox fallaron: ${err}"
                                currentBuild.result = 'UNSTABLE'
                            }
                        }
                    }
                }
            }
        }

        // Etapa 4: Deploy Simulado
        stage('Deploy a Producción (Simulado)') {
            steps {
                script {
                    // Crear carpeta "prod" y copiar build
                    sh 'mkdir -p prod && cp -r build/* prod/'
                    echo "¡Deploy simulado exitoso! Archivos copiados a /prod"
                }
            }
        }
    }

    post {
        always {
            // Publicar reportes HTML (opcional)
            publishHTML target: [
                allowMissing: true,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'prod',
                reportFiles: 'index.html',
                reportName: 'Demo Deploy'
            ]

            // Notificación por email ante fallos
             mail(
                to: 'anonimusa415@gmail.com',
                subject: "Pipeline ${currentBuild.result}: ${env.JOB_NAME}",
                body: """
                    <h2>Resultado: ${currentBuild.result}</h2>
                    <p><b>URL del Build:</b> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                    <p><b>Consola:</b> <a href="${env.BUILD_URL}console">Ver logs</a></p>
                """,
                mimeType: 'text/html'
            )

            // Limpiar workspace
            // cleanWs()
        }
    }
}
/* pipeline {
    agent any

    tools {
        nodejs 'Node_24'  // Nombre definido en Global Tool Configuration
    }

    stages {
        // Etapa 1: Checkout del código desde GitHub
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/GustavoOspinaL/ucp-app-react.git'
            }
        }

        // Etapa 2: Instalar dependencias y build del proyecto
        stage('Build') {
            steps {
                sh 'npm install'
                sh 'npm run build' // Ejecuta el build de React
            }
        }

        // Etapa 3: Ejecutar pruebas unitarias
        stage('Unit Tests') {
            steps {
                sh 'npm test -- --watchAll=false --ci --reporters=default --reporters=jest-junit' // Genera reporte JUnit
            }
            post {
                always {
                    junit 'junit.xml' // Publica reporte en Jenkins
                    archiveArtifacts artifacts: 'junit.xml', allowEmptyArchive: true
                }
            }
        }
    }

    // Post-actions (opcional)
    post {
       always {
            mail(
                to: 'anonimusa415@gmail.com',
                subject: "Build Status: ${currentBuild.currentResult}",
                body: "Job: ${env.JOB_NAME}\nEstado: ${currentBuild.currentResult}\nURL: ${env.BUILD_URL}"
            )    
        }
    }
}
 */