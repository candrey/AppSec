# Использование статистического анализатора исходного кода

## Цель: Изучить возможностиopen source SAST и обработать результаты его работы

### 1. Выбор и установка open source SAST

В качестве open source решения выберем semgrep который является легковесным инструментом статического анализа для поиска ошибок в коде.
Semgrep можно запустить несколькими способами - используя pip или docker
Для решения нашей задачи будем использовать docker
Скачиваем актуальную версию

```bash
docker pull semgrep/semgrep
```

### 2. Подготовка проекта к анализу

Для изучения возможностей semgrep будем исполльзовать проект OWASP Juice Shop

1. Склонируем репозиторий на локальный компьютер

```bash
git clone https://github.com/juice-shop/juice-shop.git --depth 1
```

2. Установим все требуемые зависимости

```bash
npm install
```

### 3. Настройка и запуск сканирования

Настроим semgrep для анализа проекта.

1. Регистрируемся на semgrep.dev и активируем токен

```bash
docker run -it -v "${PWD}:/src" semgrep/semgrep semgrep login
```

2. Переходим в папку с исходным кодом и запускаем сканирование проекта с помощью команд

```bash
cd ./juice-shop
docker run -e SEMGREP_APP_TOKEN=MY_TOKEN --rm -v ""${PWD}:/src"" semgrep/semgrep semgrep ci
```

3. После анализа, semgrep выведет список потенциальных уязвимостей, найденных нашем проекте
Список может быть очень большим, поэтому лучше просмотреть результаты сканирования на сайте: `https://semgrep.dev/orgs/candrey-personal-org/findings?repo=local_scan/src&ref=master`

### 4. Анализ результатов

После завершения сканирования проанализируем результаты.

#### Ложные срабатывания

Определим ложные срабатывания:

Semgrep сообщает о потенциальной SQL-инъекции, но параметр передаётся через ORM-фреймворк, который автоматически экранирует вводимые данные.

#### Реальные уязвимости

Определим реальные уязвимости, требующие нашего внимания.

#### sequelize-express

Уязвимость: Для создания запроса к базе данных могут использоваться ненадежные входные данные, что может привести к уязвимости при внедрении SQL. Злоумышленник может выполнить вредоносные инструкции SQL и получить несанкционированный доступ к конфиденциальным данным, изменить, удалить данные или выполнить произвольные системные команды.

#### tainted-sql-string

Уязвимость: Обнаруженный пользовательский ввод используется для создания строки SQL вручную. Обычно это плохая практика, поскольку создание вручную может случайно привести к внедрению SQL. Злоумышленник может использовать SQL-инъекцию для кражи или изменения содержимого базы данных.

#### express-sequelize-injection

Уязвимость: Обнаружен оператор sequelize, который искажен пользовательским вводом. Это может привести к внедрению SQL

#### detected-jwt-token

Уязвимость: Обнаружен JWT токен который передается в открытом виде

#### detected-generic-secret

Описание: Секреты, такие как ключи API, пароли, токены и другие конфиденциальные данные, должны храниться безопасно и никогда не передаваться открытым текстом.

Уязвимость: Обнаружены секреты которые передается в открытом виде
