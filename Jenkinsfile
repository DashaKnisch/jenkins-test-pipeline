pipeline {
    agent any
    environment {
        DB_URL = 'mysql+pymysql://usr:pwd@host:3306/db'
        DISABLE_AUTH = true
        GOOGLE_ACCESS_KEY = credentials('google-access-key-id')
    }
    stages {
        stage("Сборка") {
            steps {
                echo "Сборка приложения..."
                sh 'echo "Ключ авторизации ИТ: ${GOOGLE_ACCESS_KEY}"'
                sh 'echo "URL базы данных: ${DB_URL}"'
            }
        }
        stage("Тестирование") {
            steps {
                echo "Тестирование приложения..."
            }
        }
        stage("Деплой на стейджинг") {
            steps {
                echo "Деплой на стейджинг"
                sh "bash ./deploy staging"
                sh "bash ./smoke-tests"
            }
        }
        stage("Проверка работоспособности") {
            steps {
                input message: "Следует ли отправить на продакшн?", ok: "Да, деплоить"
            }
        }
        stage("Деплой на продакшн") {
            steps {
                sh "bash ./deploy prod"
            }
        }
    }
    post {
        always {
            echo "Выполнение конвейера успешно завершено"
        }
        cleanup {
            echo "Очистка рабочей области"
            cleanWs()
        }
    }
}
