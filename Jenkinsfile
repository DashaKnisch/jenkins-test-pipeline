pipeline {
    agent any
    environment {
        DB_URL = 'mysql+pymysql://usr:pwd@host:3306/db'
        DISABLE_AUTH = true
        // Реальный вызов зашифрованного секрета из базы данных Jenkins
        GOOGLE_ACCESS_KEY = credentials('google-access-key-id')
    }
    stages {
        stage("Сборка") {
            steps {
                echo "Сборка приложения..."
                // Маскирование секретного ключа в логах (выведет ****)
                sh "echo 'Ключ авторизации ИТ: ${GOOGLE_ACCESS_KEY}'"
                sh "echo 'URL базы данных: ${DB_URL}'"
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
            echo "Выполнение конвейера завершено"
        }
        cleanup {
            echo "Очистка рабочей области"
            cleanWs()
        }
        success {
            // Полноценное уведомление об успехе
            mail to: "d_knishenko@mail.ru",
                 subject: "Сборка проекта ${env.JOB_NAME} №${env.BUILD_NUMBER} успешна!",
                 body: "Конвейер успешно прошел все тесты и развернут на продакшн."
        }
        failure {
            // Полноценный алерт при падении
            mail to: "d_knishenko@mail.ru",
                 subject: "ALERT: Сбой в конвейере ${env.JOB_NAME} №${env.BUILD_NUMBER}",
                 body: "Сборка упала. Ссылка на логи: ${env.BUILD_URL}"
        }
    }
}
