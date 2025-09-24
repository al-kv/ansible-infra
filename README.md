# Ansible Project README

## Описание
Этот репозиторий содержит Ansible-плейбуки и роли для развертывания и настройки:

- **app**: веб-приложение с Nginx и PostgreSQL
- **mon**: мониторинговый стек (Prometheus, Grafana, Loki, Promtail, Zabbix)
- **common**: общие настройки (UFW, SSH, базовые пакеты)
- **geerlingguy.postgresql**: роль для управления PostgreSQL от Community (указана в requirements.yml)

## Структура проекта

```
├── ansible.cfg            # Конфигурация Ansible
├── inventory.ini          # Инвентори (app, mon)
├── requirements.yml       # Ansible Galaxy зависимости
├── site.yml               # Главный плейбук
├── create_client_config.yml # Дополнительный плейбук для WireGuard
├── group_vars/
│   ├── app.yml
│   ├── mon.yml
│   └── all/
│       ├── vault.yml     # Секреты (пароли, ключи) – исключён из репозитория
│       └── vault.example.yml # Шаблон секретов
├── roles/
│   ├── app/
│   ├── mon/
│   ├── common/
│   └── geerlingguy.postgresql/
└── .gitignore             # Исключения для Git
```

## Зависимости

Установите Ansible и коллекции из `requirements.yml`:

```bash
ansible-galaxy install -r requirements.yml
```

## Настройка Vault

1. Скопируйте шаблон `group_vars/all/vault.example.yml` в `group_vars/all/vault.yml`:
   ```bash
   cp group_vars/all/vault.example.yml group_vars/all/vault.yml
   ```
2. Заполните реальные значения переменных (пароли и ключи).

## Пример запуска

```bash
ansible-playbook site.yml --ask-vault-pass
```

Если используется файл с паролем по умолчанию (`vault_password_file` в ansible.cfg), то достаточно:

```bash
ansible-playbook site.yml
```

## Файлы и каталоги, исключённые из Git

С помощью `.gitignore` исключаются:

- файлы `vault.yml`
- временные файлы Ansible (`*.retry`, папки `.ansible/`)
- SSH-ключи

