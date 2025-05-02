pipeline {
    agent { label 'agent3' }

    stages {
        stage('Clonar repositorio') {
            steps {
                git url: 'https://github.com/DanielVillaneda/pruebas-go.git'
            }
        }

        stage('Verificar Go instalado') {
            steps {
                sh 'go version'
                sh 'go env'
            }
        }

        stage('Compilar ejemplos') {
            steps {
                sh '''
                for dir in $(find . -type f -name '*.go' -exec dirname {} \\; | sort -u); do
                    if [[ "$dir" != "gotypes/doc" ]]; then
                        echo "Compilando $dir..."
                        go build "$dir" || exit 1
                    else
                        echo "Saltando $dir por incompatibilidad..."
                    fi
                done
                '''
            }
        }

        stage('Ejecutar pruebas') {
            steps {
                sh '''
                mkdir -p reports
                go test -v ./... 2>&1 | go-junit-report > reports/report.xml
                '''
            }
        }
    }

    post {
        always {
            echo 'Pipeline finalizado.'
            junit 'reports/report.xml'
        }
        failure {
            echo 'Error en la compilación o pruebas.'
        }
    }
}