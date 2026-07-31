# Monitoring Lab

## О проекте

Данный проект представляет собой учебную лабораторию по мониторингу, созданную для изучения Prometheus, Grafana и Alertmanager.

Основной целью было разобраться в работе системы мониторинга без использования Docker и Kubernetes, чтобы понять принципы работы каждого компонента.

---

## Используемые технологии

- Prometheus
- Alertmanager
- Grafana
- Node Exporter
- Debian Linux
- VirtualBox
- Git
- GitHub

---

## Архитектура лаборатории

Лаборатория состоит из четырех виртуальных машин:

- Prometheus Server
- Grafana Server
- Node 1 (Node Exporter)
- Node 2 (Node Exporter)

Prometheus собирает метрики с Node Exporter и самого себя.

Grafana используется для визуализации метрик.

Alertmanager отправляет уведомления в Telegram.

---

## Схема сети

Каждая виртуальная машина использует два сетевых адаптера:

- NAT — для доступа в Интернет
- Internal Network (192.168.100.0/24) — для взаимодействия между виртуальными машинами

Для подключения к виртуальным машинам с хостовой системы используется SSH через NAT Port Forwarding.

---

## Реализованный функционал

Настроено:

- сбор метрик с Prometheus и двух Node Exporter;
- визуализация данных в Grafana;
- уведомления через Telegram;
- правила оповещения для:
  - высокой загрузки CPU;
  - высокой заполненности диска;
  - недоступности узла (Instance Down).

---

## Структура репозитория

monitoring-lab/
├── alertmanager/
│   └── alertmanager.yml
├── grafana/
│   └── grafana.ini
├── prometheus/
│   ├── prometheus.yml
│   └── rules.yml
├── screenshots/
└── README.md

---

## Используемый дашборд Grafana

Для визуализации используется стандартный дашборд сообщества:

- Node Exporter Full (Dashboard ID: 1860)

---

## Что я изучила в рамках проекта

Во время выполнения проекта были изучены:

- настройка Prometheus;
- написание правил оповещения (PromQL);
- настройка Alertmanager;
- интеграция с Telegram;
- работа Grafana;
- работа Node Exporter;
- настройка сети VirtualBox;
- SSH-доступ к виртуальным машинам;
- работа с Git и GitHub.

---

## Планы по развитию проекта

В дальнейшем планируется:

- контейнеризация проекта с использованием Docker;
- автоматизация развертывания с помощью Ansible;
- добавление CI/CD через GitHub Actions;
- перенос проекта в Kubernetes.