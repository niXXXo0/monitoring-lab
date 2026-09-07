# Monitoring Lab

Учебный DevOps-проект, в котором построена система мониторинга Linux-серверов и автоматизировано её развёртывание

Проект начинался с ручной настройки Prometheus, Grafana, Alertmanager и Node Exporter. В процессе компоненты были автоматизированы с помощью Ansible, добавлены контейнерный вариант развёртывания через Docker Compose и CI/CD на базе GitHub Actions

## Что умеет проект

Система позволяет:

- собирать метрики с Linux-серверов
- хранить и анализировать метрики с помощью Prometheus
- визуализировать данные в Grafana
- отслеживать состояние серверов
- реагировать на критические показатели
- отправлять уведомления в Telegram
- автоматически разворачивать конфигурацию с помощью Ansible
- проверять Ansible-конфигурацию через CI
- автоматически запускать deployment после успешной проверки

---

# Архитектура

В проекте используется 4 вм

| Сервер | IP | Назначение |
|---|---|---|
| Prometheus | `192.168.100.10` | Prometheus + Alertmanager |
| Node 1 | `192.168.100.11` | Node Exporter |
| Node 2 | `192.168.100.12` | Node Exporter |
| Grafana | `192.168.100.13` | Grafana |

Поток данных выглядит следующим образом:

    Node 1 ──────┐
                 │
    Node 2 ──────┼──→ Prometheus ──→ Grafana
                 │
    Prometheus ──┘
                      │
                      ↓
                 Alertmanager
                      │
                      ↓
                   Telegram

Prometheus регулярно собирает метрики с Node Exporter

Grafana получает данные из Prometheus и отображает их на дашбордах

При срабатывании alert rule Prometheus передаёт событие в Alertmanager, который отвечает за отправку уведомления

---

# Мониторинг

В Prometheus настроены правила для контроля состояния серверов

Отслеживаются:

- загрузка CPU
- свободное место на диске
- доступность серверов

При критическом состоянии создаётся алерт, который передаётся в Alertmanager

Уведомления отправляются в Telegram

---

# Два способа развёртывания

Проект поддерживает два независимых варианта деплоя

## 1. Ansible

Основной вариант развёртывания — Ansible

Ansible устанавливает и настраивает:

- Node Exporter
- Prometheus
- Alertmanager
- Grafana

Сервисы работают непосредственно на Linux-серверах и управляются через systemd

Основной playbook:

    ansible/playbooks/site.yml

Запуск:

    cd ansible
    ansible-playbook -i inventory/hosts.yml playbooks/site.yml

Секретные данные для Alertmanager хранятся с использованием Ansible Vault

---

## 2. Docker Compose

В репозитории также находится альтернативный контейнерный вариант:

    docker/

Он запускает через Docker Compose:

- Prometheus
- Alertmanager
- Grafana

Node Exporter при этом продолжает работать на отдельных Linux-серверах

Запуск:

    cd docker
    docker compose up -d

Docker-вариант не зависит от Ansible деплоя и предназначен как альтернативный способ запуска системы мониторинга

---

# CI/CD

Для автоматизации используется GitHub Actions

## Continuous Integration

При изменении проекта GitHub Actions запускает CI workflow

CI выполняет:

1. получение исходного кода
2. установку Python
3. установку Ansible
4. проверку синтаксиса основного playbook

Проверка выполняется командой:

    ansible-playbook -i inventory/hosts.yml playbooks/site.yml --syntax-check

Таким образом, потенциальная синтаксическая ошибка обнаруживается до деплоя

## Continuous Deployment

После успешного прохождения CI запускается CD workflow

CD:

1. получает тот же commit, который прошёл CI
2. запускается на self-hosted runner
3. получает пароль Ansible Vault из GitHub Secrets
4. создаёт временный файл с паролем
5. запускает Ansible playbook
6. применяет конфигурацию на серверах

Текущий CD использует Ansible deployment

Docker Compose не запускается автоматически через этот пайплайн

---

# Безопасность

Секретные данные не хранятся непосредственно в конфигурационных файлах репозитория

Используются:

- Ansible Vault для секретов Ansible
- GitHub Secrets для передачи пароля Vault в CI/CD
- отдельный файл секрета для Docker-варианта

Секретные файлы исключены из Git

---

# Структура проекта

    monitoring-lab/
    │
    ├── ansible/
    │   ├── ansible.cfg
    │   ├── inventory/
    │   ├── playbooks/
    │   └── roles/
    │
    ├── docker/
    │   ├── compose.yml
    │   ├── prometheus/
    │   ├── alertmanager/
    │   └── grafana/
    │
    ├── .github/
    │   └── workflows/
    │       ├── ci.yml
    │       └── cd.yml
    │
    ├── screenshots/
    │
    ├── .gitignore
    └── README.md

---

# Технологии

- Linux
- Prometheus
- Alertmanager
- Grafana
- Node Exporter
- Docker
- Docker Compose
- Ansible
- Ansible Vault
- systemd
- SSH
- Git
- GitHub
- GitHub Actions
- VirtualBox

---

# Grafana

Для визуализации используется дашборд **Node Exporter Full**

Dashboard ID:

    1860

Grafana подключена к Prometheus и отображает метрики, собираемые с серверов

---

# Что было сделано

Проект развивался постепенно — от ручной настройки до автоматизированного деплоя

В результате были реализованы:

- установка и настройка Prometheus
- настройка Node Exporter
- настройка Alertmanager
- интеграция с Telegram
- настройка Grafana
- создание alert rules
- контейнеризация через Docker Compose
- автоматизация установки через Ansible
- использование Ansible Vault
- CI для проверки Ansible
- CD для автоматического deployment
- self-hosted GitHub Actions runner
- безопасная работа с секретами

---

# Цель проекта

Главная цель проекта — не просто собрать систему мониторинга, а разобраться в полном цикле её эксплуатации:

    Configuration
         ↓
       Git
         ↓
    CI validation
         ↓
    CD deployment
         ↓
      Servers
         ↓
     Monitoring
         ↓
       Alerts

Проект используется как практическая лаборатория для изучения DevOps-подходов и инструментов автоматизации :)
