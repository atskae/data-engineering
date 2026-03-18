# Docker and Terraform

Following [this workshop](https://github.com/DataTalksClub/data-engineering-zoomcamp/tree/main/01-docker-terraform) from Data Engineering Zoomcamp.
* [YouTube video](https://www.youtube.com/live/lP8xXebHmuE?si=h6GOOW_6q6214PkK)

## Setup
* Launch a GitHub Codespace from this repository
    * The codespace already has Docker/Python/etc. installed
* `PS1="> "` Changes the shell's primary shell prompt in Linux

## Dockerize the Pipeline

* Change the entrypoint: `docker run -it --entrypoint=bash python:3.13.11-slim`
    * Run bash instead of Python
    * Debian-based image
* `docker ps -a` shows all the instantiated Docker containers
    * Every `docker run` will start with a new container instance with new state
        * Technically we can resume with a container instatiation from this list, but this is not encouraged
    * `docker ps -aq`: List all the IDs of the containers
    * `docker rm $(docker ps -aq)`: Removes all containers

### Preserving State
Use **volumes**, which allows the Docker container to access the host machine's files.


## Python Virtual Environemnts and Data Pipelines
**Data Pipeline**: An automated system that moves data from a source (ex: raw data) to some destination (ex. database, output file)
    * Ex. A CSV file (source) is passed into a Data Pipeline, which processes then stores the output to a Parquet file, PostgreSQL Database, and a Data Warehouse (destinations)
* [NYC Taxi Dataset](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

Create a separate Python virtual environment (venv) with `uv`:
 ```
 pip install uv
 ```
* Create a new venv with Python v3.13: `uv init --python 3.13`
* Use the Python in the venv `uv run python -V`
* Check the Python in the venv: `uv run which python`
    ```
    > uv run which python
    /workspaces/data-engineering/docker-terraform/data-pipeline/.venv/bin/python
    ```
* Add new Python libraries to the venv: `uv add pandas pyarrow`
    * Adds `pandas` and `pyarrow` Python libraries
* Run Python script in venv: `uv run python pipeline.py`

Docker
* `docker build -t test:pandas .`: Build Docker image in the current directory (`.`) and label it with the repository `test` and tag `pandas`
* `docker run -it --entrypoint=bash test:pandas`: Run the image that we built to instantiate a Docker container in interactive mode (`-it`) and entrypoint with bash
    * We can add `--rm` to the `docker run` command if we do not want this container state saved
    ```
    > docker run -it --entrypoint=bash test:pandas
    root@9b8c25a2dfef:/code# ls
    pipeline.py
    root@9b8c25a2dfef:/code# pwd
    /code
    ```
* Add an entrypoint in the Dockerfile to automatically start the pipeline when the container is instantiated:
    ```Dockerfile
    ENTRYPOINT ["python", "pipeline.py"]
    ```

### Use `uv`

We can copy the `uv` binary from the official `uv` Docker image into our Docker image at `/bin/`:
```Dockerfile
COPY --from=ghcr.io/astral-sh/uv:latest /uv /bin/
```
We can then remove installing with `pip`.

Install the pipeline dependencies from the lockfile:
```Dockerfile
RUN uv sync --locked
```

Then change the entry point in the Dockerfile to:
```Dockerfile
ENTRYPOINT ["uv", "run", "python", "pipeline.py"]
```
to use new venv inside the Docker container.

We can also add this to the Dockerfile:
```Dockerfile
ENV PATH="/code/.venv/bin:$PATH"
```
Then we do not need `["uv", "run"]` in the entrypoint - the container will automatically use Python in the venv.
