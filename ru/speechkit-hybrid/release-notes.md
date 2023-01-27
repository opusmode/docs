# Релизы {{ speechkit-full-name }} Hybrid

Здесь будет опубликована информация об обновлениях версий образов {{ sk-hybrid-name }}.

## Текущая версия {#current}

### Релиз 05.12.22 {#051222}

1. Добавлена возможность задавать уровни [логирования](operations/logging.md) контейнеров {{ sk-hybrid-name }}. 
1. Формат времени логов приведен к одному виду во всех компонентах.
1. Исправлена часть проблем, мешавших запустить агент из-за повреждения `swaydb`.
1. Добавлена возможность включить [терминирование TLS](operations/tls-proxy.md).
1. Установлены актуальные обновления безопасности.
1. Изменился скрипт запуска служб внутри контейнеров. Улучшено поведение системы при `kill -15`: теперь выключение происходит с graceful shutdown.