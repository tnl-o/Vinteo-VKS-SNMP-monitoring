# Vinteo VCS SNMP Monitoring

Конфигурация для мониторинга Vinteo VCS через SNMP Exporter, Prometheus и Grafana.
![alt text](https://github.com/tnl-o/Vinteo-VKS-SNMP-monitoring/edit/main/2025-12-15_15-24.png?raw=true)
## Структура проекта

```
Vinteo_SNMP_grafana/
├── README.md
├── snmp_exporter/
│   ├── snmp.yml          # Конфигурация SNMP Exporter
│   └── dashboard.json    # Дашборд Grafana
└── prometheus/
    └── prometheus.yml.example  # Пример конфигурации Prometheus
```

## Конфигурация

### 1. SNMP Exporter

**Файл:** `snmp_exporter/snmp.yml`  
**Куда:** `/etc/snmp_exporter/snmp.yml`

```yaml
auths:
  vinteo_v2c_public:
    version: 2
    community: YOUR_COMMUNITY_STRING  # Замените на ваш community string
```

### 2. Prometheus

**Файл:** `prometheus/prometheus.yml.example`  
**Куда:** Добавить в ваш `prometheus.yml`

```yaml
  - job_name: 'vinteo_vcs'
    scrape_interval: 30s
    metrics_path: /snmp
    params:
      module: [vinteo_vcs_web]
      auth: [vinteo_v2c_public]
    static_configs:
      - targets:
        - '192.168.16.10'  # IP вашего Vinteo сервера
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: 'localhost:9116'  # Или IP где запущен SNMP Exporter
```

### 3. Grafana

**Файл:** `snmp_exporter/dashboard.json`  
**Куда:** Импортировать через Grafana UI (Dashboards → Import → Upload JSON file)

**Важно:** Убедитесь, что в Grafana настроен Prometheus datasource. В дашборде используется datasource с UID `Prometheus-SNMP` - при необходимости измените в панелях или используйте свой datasource.

## Метрики

### Основные метрики
- `vinteo_vcs_web_conferences_total` - Всего конференций
- `vinteo_vcs_web_conferences_total_active` - Активные конференции
- `vinteo_vcs_web_conferences_total_online_clients` - Онлайн клиентов
- `vinteo_vcs_web_licenses_fullhd` - FullHD лицензии
- `vinteo_vcs_web_licenses_hd` - HD лицензии
- `vinteo_vcs_web_licenses_vga` - VGA лицензии
- `vinteo_vcs_web_licenses_audio` - Audio лицензии
- `vinteo_vcs_web_records_active` - Активные записи
- `vinteo_vcs_web_records_stored` - Всего записей
- `vinteo_vcs_web_webcasts_hls_active` - Активные HLS трансляции
- `vinteo_vcs_web_webcasts_youtube_active` - Активные YouTube трансляции
- `vinteo_vcs_web_replication_actualised` - Статус репликации (1 = актуальна)

### Метрики конференций (с label `vvw_conference_number`)
- `vinteo_vcs_web_conference_number` - Номер конференции
- `vinteo_vcs_web_conference_description` - Описание
- `vinteo_vcs_web_conference_status` - Статус (1 = активна)
- `vinteo_vcs_web_conference_temporary` - Временная (1 = да)
- `vinteo_vcs_web_conference_shutdown` - Будет завершена (1 = да)
- `vinteo_vcs_web_conference_participants` - Количество участников
- `vinteo_vcs_web_conference_online` - Онлайн клиентов
