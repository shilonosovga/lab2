# Поддерживаемая версия Python на Debian 13
FROM python:3.13-slim-trixie

# Настройки Python и pip
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

# Отдельный пользователь без root-привилегий
RUN groupadd --system --gid 10001 app \
 && useradd --system --uid 10001 --gid app \
      --no-create-home --shell /usr/sbin/nologin app

# Установка зависимостей
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt \
 && pip install --no-cache-dir --upgrade setuptools==78.1.1 \
 && rm -rf /usr/local/lib/python3.13/site-packages/pkg_resources/tests
# Копирование кода приложения
COPY app.py .

USER app
EXPOSE 5000

# Проверка состояния приложения
HEALTHCHECK --interval=30s --timeout=3s --start-period=10s --retries=3 \
  CMD ["python", "-c", "import urllib.request as u; u.urlopen('http://localhost:5000/health')"]

# Запуск через production WSGI-сервер
CMD ["gunicorn", "--bind", "0.0.0.0:5000", "--workers", "2", "--access-logfile", "-", "app:app"]
