# Практикум по использованию Nexus Repository для скачивания библиотек

## Цель: научиться использовать менеджер репозиториев Nexus Repository

### 1. Установка Nexus Repository с использованием Docker

Запустить Nexus Repository в docker:

```bash
docker run -d -p 8081:8081 --name nexus sonatype/nexus3
```

Откроем Nexus в браузере используя адрес [http:\\\localhost:8081](http:\\localhost:8081)

![Sonatype Nexus Repository](<Screenshot_20250312_143102.png>)

Для доступа к Nexus, по умолчанию используется логин: admin, пароль (потребуется сменить при первом подключении) можно посмотреть в контейнере командой:

```bash
docker exec -it nexus cat sonatype-work/nexus3/admin.password
```

### 2. Настройка репозиториев в Nexus

Шаг 1: Создание репозиториев

1. Переходим в раздел Repositories.
2. Создадим репозитории для dnf:

- yum (proxy)
Заполняем параметры:
Name: dnf-proxy
Remote storage: Указываем URL официального репозитория для Fedora: `https://mirror.yandex.ru/fedora/linux/releases/41/Server/x86_64/os/`

![Созданные репозитории](<Screenshot_20250313_095114.png>)

Шаг 2: Настройка прав доступа

Дополнительно можно настроить права доступа, мы в лабораторных условиях будем использовать анонимный доступ.

### 3. Подключение ВМ к Nexus

Шаг 1: Настраиваем dnf

Добавляем репозиторий Nexus в fedora.repo

Отредактируем файл /etc/apt/sources.list:

```bash
sudo vim /etc/yum.repos.d/fedora.repo 
```

Добавляем строку для подключения к репозиторию Nexus:

```bash
#metalink=https://mirrors.fedoraproject.org/metalink?repo=fedora-$releasever&arch=$basearch
baseurl=http://127.0.0.1:8081/repository/dnf-proxy/
```

### 4. Проверка работы Nexus

- 1 Установка пакетов через dnf

Установите пакет через dnf:
Попробуем установить nmap-ncat:

```bash
sudo dnf install nmap-ncat
```

Убедимся, что пакет скачивается через Nexus, для этого перейдём в веб-интерфейс Nexus, откройтем репозиторий dnf-proxy.
Убедимся, что пакет nmap-ncat появился в списке загруженных артефактов.
![dnf repository](<Screenshot_20250313_095129.png>)

### 5. Результаты

Nexus Repository успешно установлен и настроен.
dnf подключен к Nexus и пакеты скачиваются корректно.
