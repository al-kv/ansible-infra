# ansible-infra

“Ansible playbooks and roles to automate WireGuard VPN and monitoring stack deployment.”

## Описание

Этот репозиторий содержит Ansible-плейбуки и роли для развертывания и настройки:

- **app**: веб-приложение с Nginx и PostgreSQL  
- **mon**: мониторинговый стек (Prometheus, Grafana, Loki, Promtail, Zabbix)  
- **common**: общие настройки (UFW, SSH, базовые пакеты)  
- **geerlingguy.postgresql**: роль для управления PostgreSQL от Community  

## Структура проекта

```
├── ansible.cfg              # Конфигурация Ansible
├── inventory.ini            # Инвентори (app, mon)
├── requirements.yml         # Ansible Galaxy зависимости
├── site.yml                 # Главный плейбук
├── create_client_config.yml # Дополнительный плейбук для WireGuard
├── group_vars/
│   ├── app.yml
│   ├── mon.yml
│   └── all/
│       ├── vault.example.yml # Шаблон секретов
│       └── vault.yml         # Секреты (пароли, ключи) – исключён из репозитория
├── roles/
│   ├── app/
│   ├── mon/
│   ├── common/
│   └── geerlingguy.postgresql/
└── .gitignore               # Исключения для Git
```

## Зависимости

Установите Ansible и коллекции из `requirements.yml`:

```bash
ansible-galaxy install -r requirements.yml
```

## Настройка Vault

1. Скопируйте шаблон:
   ```bash
   cp group_vars/all/vault.example.yml group_vars/all/vault.yml
   ```
2. Заполните реальные значения переменных (пароли и ключи) в `group_vars/all/vault.yml`.

## Пример запуска

Если требуется ввод пароля для Vault:
```bash
ansible-playbook site.yml --ask-vault-pass
```

Если в `ansible.cfg` указан `vault_password_file`:
```bash
ansible-playbook site.yml
```

## Исключённые файлы и каталоги

Файл `.gitignore` содержит правила для исключения:
- `group_vars/all/vault.yml`
- временных файлов Ansible (`*.retry`, папка `.ansible/`)
- SSH-ключей
