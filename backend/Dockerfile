# Dockerfile for CBMo4ers Backend

# Use a slim Python base image for smaller size
FROM python:3.12-slim

# Set environment variables
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

# Install system dependencies required for some Python packages (e.g., psutil)
RUN apt-get update && apt-get install -y \
    gcc \
    g++ \
    && rm -rf /var/lib/apt/lists/*

# Set working directory
WORKDIR /app

# Copy requirements first to leverage Docker cache
COPY requirements.txt .

# Install Python dependencies
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -r requirements.txt

# Copy the rest of the application code
COPY . .

# Create a non-root user for security best practices
RUN addgroup --system --gid 1001 cbmo4ers && \
    adduser --system --uid 1001 --gid 1001 cbmo4ers

# Change ownership of the application directory
RUN chown -R cbmo4ers:cbmo4ers /app

# Switch to the non-root user
USER cbmo4ers

# Expose the port the application will run on
EXPOSE 5001

# Health check for container orchestration platforms
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
    CMD python -c "import requests; requests.get('http://localhost:5001/api/health', timeout=5)" || exit 1

# Command to run the application using Gunicorn
CMD ["gunicorn", "--bind", "0.0.0.0:5001", "--workers", "4", "--timeout", "60", "--max-requests", "1000", "--max-requests-jitter", "100", "app:app"]
