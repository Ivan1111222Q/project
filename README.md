# Effective Mobile - DevOps Test Project

Простое веб-приложение с nginx в качестве reverse proxy, развернутое в Docker-контейнерах.

## Структура проекта

```
├── backend/
│   ├── Dockerfile
│   └── app.py
├── nginx/
│   └── nginx.conf
├── docker-compose.yml
└── README.md
```

## Описание архитектуры

```
┌─────────────┐
│   Client    │
│  (Browser)  │
└──────┬──────┘
       │ HTTP :80
       ▼
┌─────────────┐      ┌─────────────┐
│    Nginx    │─────▶│   Backend   │
│  (Port 80)  │      │  (Port 8080)│
│             │◀─────│             │
└─────────────┘      └─────────────┘
   (exposed)         (internal only)
```

**Как это работает:**

1. **Backend** - Python HTTP-сервер, слушает порт 8080 внутри Docker-сети
2. **Nginx** - Reverse proxy, принимает запросы на порт 80 и проксирует их на backend
3. **Docker Compose** - Управляет обоими сервисами и настраивает сеть между ними
4. Backend доступен только внутри Docker-сети, наружу пробрасывается только порт 80 nginx

## Запуск проекта

### Требования

- Docker
- Docker Compose

### Инструкция

1. Клонируйте репозиторий (если необходимо):
   ```bash
   git clone <repository-url>
   cd project
   ```

2. Запустите проект:
   ```bash
   docker-compose up -d
   ```

   Эта команда:
   - Соберет образ backend из Dockerfile
   - Запустит контейнеры backend и nginx
   - Настроит сеть между ними
   - Пробросит порт 80 nginx на хост

3. Проверьте статус контейнеров:
   ```bash
   docker-compose ps
   ```

## Проверка работоспособности

Выполните запрос к приложению:

```bash
curl http://localhost
```

**Ожидаемый ответ:**
```
Hello from Effective Mobile!
```

### Дополнительные проверки

Проверка логов backend:
```bash
docker-compose logs backend
```

Проверка логов nginx:
```bash
docker-compose logs nginx
```

Проверка работы nginx напрямую:
```bash
curl -v http://localhost
```

## Остановка проекта

```bash
docker-compose down
```

Для полной очистки (включая образы):
```bash
docker-compose down --rmi all
```

## Использованные технологии

- **Python 3.11** - Backend приложение (http.server)
- **Nginx 1.25** - Reverse proxy
- **Docker** - Контейнеризация
- **Docker Compose** - Оркестрация контейнеров

## Особенности реализации

- ✅ Backend запускается от непривилегированного пользователя (appuser)
- ✅ Используется отдельная Docker-сеть для изоляции
- ✅ Backend не публикует порт наружу (доступен только в Docker-сети)
- ✅ Nginx передает стандартные proxy-заголовки (Host, X-Real-IP, X-Forwarded-For)
- ✅ Явные имена контейнеров и сервисов
- ✅ Минимальный размер образов (alpine/slim версии)
- ✅ Автоматический перезапуск контейнеров при сбое

