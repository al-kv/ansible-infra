# ansible-infra

“Ansible playbooks and roles to automate WireGuard VPN, application and monitoring stack deployment.”

## Описание

Этот репозиторий содержит набор Ansible-плейбуков и ролей для развертывания и настройки:

- **vpn**: настройка WireGuard VPN сервера  
- **app**: веб-приложение с Nginx и PostgreSQL  
- **mon**: мониторинговый стек (Prometheus, Grafana, Loki, Promtail, Zabbix)  
- **common**: общие настройки (UFW, SSH, базовые пакеты)  
- **geerlingguy.postgresql**: роль для управления PostgreSQL от Community  

## Структура проекта

```
├── ansible.cfg              # Конфигурация Ansible
├── inventory.ini            # Инвентори (app, mon)
├── requirements.yml         # Зависимости Ansible Galaxy
├── site.yml                 # Главный плейбук
├── create_client_config.yml # Плейбук для создания конфигов WireGuard
├── group_vars/
│   ├── app.yml
│   ├── mon.yml
│   └── all/
│       ├── vault.example.yml # Шаблон секрета
│       └── vault.yml         # Секреты (не в репо)
├── roles/
│   ├── vpn/                  # роли для WireGuard
│   ├── app/                  # роли веб-приложения
│   ├── mon/                  # роли мониторинга
│   ├── common/               # базовые настройки
│   └── geerlingguy.postgresql/ # ролевая коллекция PostgreSQL
└── .gitignore               # Исключения файлов
```

## Зависимости

Установите коллекции Ansible из `requirements.yml`:

```bash
ansible-galaxy install -r requirements.yml
```

## Настройка Vault

1. Скопируйте шаблон:
   ```bash
   cp group_vars/all/vault.example.yml group_vars/all/vault.yml
   ```
2. Заполните `group_vars/all/vault.yml` реальными паролями и ключами.

## Пример запуска

- С вводом пароля Vault:
  ```bash
  ansible-playbook site.yml --ask-vault-pass
  ```
- Если в `ansible.cfg` указан `vault_password_file`:
  ```bash
  ansible-playbook site.yml
  ```

## Исключённые файлы и каталоги

В `.gitignore` прописано исключение для:

- `group_vars/all/vault.yml`
- временных файлов Ansible (`*.retry`, папка `.ansible/`)
- SSH-ключей