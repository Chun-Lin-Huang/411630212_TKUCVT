FROM python:3.12-slim

WORKDIR /app

RUN useradd -u 1000 -m appuser

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY --chown=appuser:appuser app.py .

USER appuser

EXPOSE 8080

CMD ["python", "app.py"]