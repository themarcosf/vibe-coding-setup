### Environment setup #########################################################

FROM python:3.12-slim AS base

# Disable interactive prompts during apt
ENV DEBIAN_FRONTEND='noninteractive'

# Disable .pyc bytecode generation
ENV PYTHONDONTWRITEBYTECODE=1

# Turn off buffering for easier container logging
ENV PYTHONUNBUFFERED=1

# Project root folder
ENV PROJECT_ROOT='/var/app'

# Disable pip version check
ENV PIP_DISABLE_PIP_VERSION_CHECK=1

# Disable pip cache
ENV PIP_NO_CACHE_DIR=1

# Set ipdb as debugger for breakpoint()
ENV PYTHONBREAKPOINT="ipdb.set_trace"

### System packages ###########################################################

# Update system and install helpers
RUN set -eux; \
    apt-get update; \
    apt-get install -y --no-install-recommends curl; \
    rm -rf /var/lib/apt/lists/*

### Create app user and workspace #############################################

# Create system group and a dedicated non-root user (app)
RUN groupadd -r app && useradd -m -r -g app -s /bin/bash app

# Set home directory
ENV HOME=/home/app

# Set working directory
WORKDIR /var/app

# Change ownership of project files to app user
RUN chown -R app:app /home/app /var/app

### Python dependencies #######################################################

COPY requirements.txt /var/app/requirements.txt

# Upgrade pip, setuptools, and wheel
RUN pip install --upgrade pip setuptools wheel; \
    pip install --no-cache-dir ipython==9.5.0; \
    pip install --no-cache-dir pip-tools==7.5.0; \
    pip install --no-cache-dir ipdb; \
    pip install --no-cache-dir -r requirements.txt

### App setup #################################################################

# Switch to app user
USER app

# Set default profile to DEV
ENV PROFILE=DEV

# Add init.sh to profile.d for shell environment setup
COPY docker/init.sh /etc/profile.d/init.sh

COPY src /var/app/src

CMD [ "python", "-m", "src.main" ]