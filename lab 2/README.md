# Управление секретами с использованием Ansible Vault

## Цель

Разработать процесс безопасного управления секретами с использованием Ansible Vault для шифрования и дешифрования конфиденциальной информации.

---

## 1. Определеняем список секретов тербуемых для нашего проекта

Для нашего проекта потребуется безопасно хранить следующие секреты:

- Пароль для подключения к почтовому серверу
- Пароль для подключения к базе данных

## 2. Настройка Ansible Vault

1. **Создаём зашифрованный файл с помощью утилиты Ansible Vault**:

   ```bash
   ansible-vault create secrets.yaml
   ```

    - Команда запросит пароль для шифрования, после чего откроется текстовый редактор по умолчанию (nano/vim).
    - По окончании работы с редактором файл будет сохранён в зашифрованном виде.

2. **Автоматическая расшифровка зашифрованного файла**:

   Для автоматической расшифровки файла можно использовать несколько путей:
   - Запускаем плейбук с указанием опции ```--vault-password-file```
    ```ansible-playbook --vault-password-file /path/to/.vault-password playbook.yml```
   - Установить переменную среды ANSIBLE_VAULT_PASSWORD_FILE с путем к файлу пароля:
    ```export ANSIBLE_VAULT_PASSWORD_FILE=/path/to/.vault-password```
   - Добавить переменную в локальном файле ```~/.ansible.cfg```

     ```ini
      [defaults]
      vault_password_file = /path/to/.vault-password
     ```

## 3. Процесс создание зашифрованных файла

Создадим зашифрованный файл с нашими секретными данными.

```bash
ansible-vault create secrets.yml
```
После ввода пароля откроется текстовый редактор. Добавляем наши секретные данные:

```yaml
ansible_become_pass: SuperPass
email_password: Qwe123
pg_password: Jieghaij0OoNg8nait
```

Этот файл будет зашифрован с помощью Ansible Vault, и его содержимое будет недоступно без использования соответствующего ключа.

## 4. Развёртывание микросервисов с использованием зашифрованных данных

Для того чтобы использовать зашифрованные секреты в процессе развёртывания будем использовать темплейт

`templates/env.j2`

```ini
  PYTHON_ENV=dev
  DB_TYPE=postgresql
  DB_NAME=fury
  DB_USER=hng
  DB_PASSWORD="{{ pg_password }}"
  DB_HOST="localhost"
  DB_PORT=5432
  EMAIL_SERVER="localhost"
  SMTP_PORT=25
  SMTP_USER=User
  SMTP_PASSWORD="{{ email_password }}"
```

Плейбук `deploy_microservices.yml`:

```yaml
---
- name: Deploy microservices
  hosts: all
  vars_files:
    - secrets.yml

  tasks:
  - name: Create virtual environment
    ansible.builtin.command:
      cmd: python3 -m venv /opt/otus/.venv
      creates: /opt/otus/.venv
    become: yes
    become_user: otus
    register: venv_created

  - name: Install project dependencies
    ansible.builtin.pip:
      requirements: /opt/otus/requirements.txt
      virtualenv: /opt/otus/.venv
    become: yes
    become_user: otus

  - name: Create an env file
    ansible.builtin.template:
      src: env.j2
      dest: /opt/otus/.env
    become: yes
    become_user: otus
```

## 5. Подходы к безопасному хранению и управлению ключами доступа

Для обеспечения безопасности хранения и управления ключами доступа рекомендуется следовать следующим практикам:

1. **Использование защищённых хранилищ**: Хранение чувствительной информации с использованием специальных возожностей GitLab, GitHub, где доступ строго ограничен и управляется ролями пользователей.
2. **Регулярная смена ключей**: Периодическая смена ключей шифрования и обновление паролей помогает минимизировать риски утечки информации.
3. **Контроль доступа**: Ограничить доступ к файлам с ключами шифрования только тем пользователям, которым действительно необходим доступ.
4. **Журналирование действий**: Ведение логов всех операций с ключами шифрования и доступом к ним.

## 6. Документация по шифрованию, дешифрованию и управлению секретами

Документирование процессов шифрования, дешифрования и управления секретами является важной частью обеспечения безопасности.

## Шифрование секретов

### Создание нового зашифрованного файла

```bash
ansible-vault create secrets.yml
```

### Редактирование существующего зашифрованного файла

```bash
ansible-vault edit secrets.yml
```

## Дешифрование секретов

### Просмотр шифрованного файла

```bash
ansible-vault view secrets.yml
```

### Постоянное дешифрование файла

```bash
ansible-vault decrypt secrets.yml
```

#### Запуск плейбуков с использованием зашифрованных файлов

```bash
ansible-playbook deploy_microservices.yml --ask-vault-pass
```
