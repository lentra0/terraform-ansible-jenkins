# Terraform + Ansible + Jenkins Demo

## Файловая структура и назначение

### 1. Terraform конфигурация
- [`infrastructure/terraform/main.tf`](infrastructure/terraform/main.tf):  
  Главный конфигурационный файл, определяющий:
  - Провайдер Yandex Cloud
  - Виртуальные машины для web-сервера и Jenkins
  - Сетевые настройки (VPC и подсети)
  - Output переменные для IP-адресов
- [`infrastructure/terraform/variables.tf`](infrastructure/terraform/variables.tf):  
  Параметры для настройки окружения:
  - Токены доступа Yandex Cloud
  - ID облака и каталога
  - Путь к SSH-ключу

### 2. Ansible плейбуки
- [`infrastructure/ansible/setup.yml`](infrastructure/ansible/setup.yml):  
  Настройка веб-сервера:
  - Установка Nginx
  - Создание веб-директории
  - Развертывание шаблонного контента
- [`infrastructure/ansible/jenkins.yml`](infrastructure/ansible/jenkins.yml):  
  Установка и настройка Jenkins:
  - Инсталляция Java
  - Добавление репозитория Jenkins
  - Запуск и настройка службы
- [`infrastructure/ansible/templates/index.html.j2`](infrastructure/ansible/templates/index.html.j2):  
  Jinja2 шаблон для главной страницы:
  - Динамическое отображение имени хоста
  - Вывод времени деплоя

### 3. Интеграционные файлы
- [`infrastructure/ansible/inventory.yml`](infrastructure/ansible/inventory.yml):  
  Динамический инвентарь, использующий выходные переменные Terraform:
  - Автоматическое определение IP-адресов
  - Группировка хостов по ролям (webservers/jenkins)

### 4. CI/CD
- [`Jenkinsfile`](Jenkinsfile):  
  Pipeline для автоматизации процессов:
  ```groovy
  stage('Terraform Validate') {
    sh 'terraform -chdir=${TF_DIR} validate'
  }
  ```
  Этапы:
  1) Валидация Terraform
  2) Проверка Ansible линтером
  3) Деплой в staging
  4) Очистка инфраструктуры (опционально)

### 5. Документация
- [`README.md`](README.md):  
  Полное руководство по проекту:
  - Архитектура решения
  - Инструкции по запуску
  - Описание навыков для работодателей

## Принцип работы
1. Terraform создает инфраструктуру в Yandex Cloud
2. Output переменные Terraform передаются в Ansible inventory
3. Ansible настраивает серверы согласно плейбукам
4. Jenkinsfile автоматизирует весь процесс:
   - Проверка конфигураций
   - Развертывание
   - Управление жизненным циклом
