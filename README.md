# Домашнее задание к занятию "Что такое DevOps. СI/СD" - Филиппов Константин


### Инструкция по выполнению домашнего задания

   1. Сделайте `fork` данного репозитория к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/git-hw или  https://github.com/имя-вашего-репозитория/7-1-ansible-hw).
   2. Выполните клонирование данного репозитория к себе на ПК с помощью команды `git clone`.
   3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
      - впишите вверху название занятия и вашу фамилию и имя
      - в каждом задании добавьте решение в требуемом виде (текст/код/скриншоты/ссылка)
      - для корректного добавления скриншотов воспользуйтесь [инструкцией "Как вставить скриншот в шаблон с решением](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md)
      - при оформлении используйте возможности языка разметки md (коротко об этом можно посмотреть в [инструкции  по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md))
   4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`);
   5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
   6. Любые вопросы по выполнению заданий спрашивайте в чате учебной группы и/или в разделе “Вопросы по заданию” в личном кабинете.
---

### Задание 1

`Приведите ответ в свободной форме........`

`При необходимости прикрепитe сюда скриншоты
![jenkins](img/1.png) [nexus](img/2.png) `



---

### Задание 2

Поле для вставки кода...

```
pipeline {
    agent any
    environment {
        GOROOT = '/usr/lib/go'   // поправьте под ваш путь, если нужно
        GOPATH = "${env.WORKSPACE}/.go"
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', 
                    url: 'https://github.com/NekoYo1/sdvps-materials-homework_fork'
            }
        }
        stage('Test') {
            steps {
                sh 'go test ./...'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh "docker build -t ubuntu-bionic:8082/hello-world:${env.BUILD_ID} ."
            }
        }
    }
}
```

`При необходимости прикрепитe сюда скриншоты
![jenkins](img/3.png)`



---

### Задание 3

`Приведите ответ в свободной форме........`

`При необходимости прикрепитe сюда скриншоты
![jenkins](img/4.png) [nexus](img/5.png) [jenkins](img/6.png)`
 

```
pipeline {
    agent any

    environment {
        // Путь к raw-репозиторию Nexus (проверьте порт и название репозитория)
        NEXUS_URL    = 'http://192.168.56.10:8081/repository/go-binary-storage'
        // Название итогового бинарного файла
        APP_NAME     = 'hello-world'
        // Переменная, куда будут подставлены учётные данные из Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/NekoYo1/sdvps-materials-homework_fork'
            }
        }

        stage('Test') {
            steps {
                sh 'go test ./...'
            }
        }

        stage('Build Go Binary') {
            steps {
                // Статическая сборка, чтобы не зависеть от окружения
                sh 'CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o ${APP_NAME} .'
            }
        }

        stage('Upload to Nexus') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'nexus-raw-credentials',
                    usernameVariable: 'NEXUS_USER',
                    passwordVariable: 'NEXUS_PASSWORD'
                )]) {
                    sh '''
                        echo "Uploading ${APP_NAME} to Nexus..."
                        curl --fail -u ${NEXUS_USER}:${NEXUS_PASSWORD} \
                             --upload-file ${APP_NAME} \
                             ${NEXUS_URL}/${APP_NAME}-${BUILD_ID}
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "✅ Artifact has been uploaded to ${NEXUS_URL}/${APP_NAME}-${BUILD_ID}"
        }
        failure {
            echo "❌ Build failed. Check the logs."
        }
    }
}
```

`При необходимости прикрепитe сюда скриншоты
![Название скриншота](ссылка на скриншот)`

