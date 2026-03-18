# Base image
FROM python:3.13.11-slim

# Set the *current directory* used for the next Docker commands
# WORKDIR is *not* the home directory!
WORKDIR code/

# Install the dependencies of the data pipeline
COPY --from=ghcr.io/astral-sh/uv:latest /uv /bin/

# Copy the files required by uv to the home directory in the container
COPY .python-version pyproject.toml uv.lock ./

# Install the pipeline dependencies from the lock file
RUN uv sync --locked

# Copy the pipeline script into the home directory in the Docker container
# The script gets copied to code/ in the Docker container
COPY pipeline.py .

# Automatically use the Python in the venv
ENV PATH="/code/.venv/bin:$PATH"

# Start the pipeline when the container is instantiated
ENTRYPOINT ["python", "pipeline.py"]
