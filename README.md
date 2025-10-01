# ansible-infra

Ansible playbooks and roles to automate WireGuard VPN, application and monitoring stack deployment.

## Описание

Этот репозиторий содержит набор Ansible-плейбуков и ролей для развертывания и настройки:
- vpn: настройка WireGuard VPN сервера
- app: веб-приложение с Nginx и PostgreSQL
- mon: подготовка окружения для мониторинга (директории, права)
- database: настройка PostgreSQL и управление базой данных
- monitoring: запуск Prometheus и Node Exporter в контейнерах
- grafana: запуск Grafana в контейнере
- common: общие настройки (UFW, SSH, базовые пакеты, неинтерактивный APT, unattended-upgrades)
- geerlingguy.postgresql: роль сообщества для управления PostgreSQL

## Структура проекта

```
├── ansible.cfg
├── inventory.ini
├── requirements.yml
├── site.yml
├── create_client_config.yml
├── group_vars/
│   ├── app.yml
│   ├── mon.yml
│   └── all/
│       ├── vault.example.yml
│       └── vault.yml         # Секреты (не в репо)
├── roles/
│   ├── vpn/
│   ├── app/
│   ├── mon/
│   ├── database/
│   ├── monitoring/
│   ├── grafana/
│   ├── common/
│   └── geerlingguy.postgresql/
└── .gitignore
```

## Зависимости

Установите зависимости Ansible Galaxy и коллекции:

```bash
ansible-galaxy install -r requirements.yml
```

Файл `requirements.yml` содержит:

```yaml
roles:
  - src: geerlingguy.postgresql
collections:
  - name: community.docker
    version: ">=2.0.0"
```

## Настройка Vault

1. Скопируйте шаблон:
   ```bash
   cp group_vars/all/vault.example.yml group_vars/all/vault.yml
   ```
2. Заполните `group_vars/all/vault.yml` реальными паролями и ключами.

## Пример запуска

- С запросом пароля Vault:
  ```bash
  ansible-playbook site.yml --ask-vault-pass
  ```
- Если в `ansible.cfg` указан `vault_password_file`:
  ```bash
  ansible-playbook site.yml
  ```

## Компоненты
- WireGuard VPN
- Веб-приложение (Nginx + PostgreSQL)
- Подготовка окружения:
  - mon (директории и права)
  - geerlingguy.postgresql (PostgreSQL)
- Мониторинговый стек:
  - Prometheus
  - Node Exporter (порт 9101)
  - Grafana
- Классический мониторинг:
  - Zabbix Server + Zabbix Agent (UI на svr1)

## Проверка

Для предварительной проверки без изменений выполните:

```bash
ansible-playbook site.yml --check
```

После успешного выполнения проверьте:
- http://<app_host>/            (веб-приложение)
- http://<app_host>/health      (health check)
- http://svr2.vpn.local:9090/targets      (Prometheus)
- http://svr2.vpn.local:9101/metrics      (Node Exporter)
- http://svr2.vpn.local:3000/             (Grafana)
- http://svr1.vpn.local/                  (Zabbix Frontend)
- http://svr1.vpn.local/health            (Zabbix Frontend health)
---

**Примечание:** в `.gitignore` исключены:
- `group_vars/all/vault.yml`
- временные файлы Ansible (`*.retry`, `.ansible/`)
- SSH-ключи

