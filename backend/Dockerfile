FROM python:3.11-slim


WORKDIR /app


RUN useradd -m -u 1000 appuser && \
    chown -R appuser:appuser /app


COPY app.py /app/app.py


RUN chown appuser:appuser /app/app.py && \
    chmod +x /app/app.py


USER appuser


EXPOSE 8080


CMD ["python3", "app.py"]

