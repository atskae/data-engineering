# Base image
FROM python:3.13.11-slim

# Install the dependencies of the data pipeline
RUN pip install pandas pyarrow

# Set home directory
WORKDIR code/

# Copy the pipeline script into the home directory in the Docker container
# The script gets copied to code/ in the Docker container
COPY pipeline.py .

# Start the pipeline when the container is instantiated
ENTRYPOINT ["python", "pipeline.py"]
