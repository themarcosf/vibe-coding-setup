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

# Include both src and project root to ease relative imports
ENV PYTHONPATH="${PROJECT_ROOT}/src:${PROJECT_ROOT}"

# Avoid errors when uv can't hardlink
ENV UV_LINK_MODE='copy'

### System packages ###########################################################

# Update system and install helpers
RUN set -eux; \
    apt-get update; \
    apt-get install -y --no-install-recommends curl git; \
    apt-get -y autoremove; \
    apt-get -y autoclean; \
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

# Upgrade python tools and install dependencies
RUN pip install --upgrade pip setuptools wheel; \
    pip install --no-cache-dir ipython==9.5.0; \
    pip install --no-cache-dir pip-tools==7.5.0; \
    pip install --no-cache-dir ipdb==0.13.13; \
    pip install --no-cache-dir uv==0.8.17; \
    pip install --no-cache-dir -r requirements.txt

### Setup NodeJS ##############################################################

# Install Node with NodeSource
RUN curl -fsSL https://deb.nodesource.com/setup_22.x | bash -; \
    apt-get install -y nodejs; \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Install node packages globally
RUN npm install -g typescript@5.9.2
RUN npm install -g tsx@4.20.5
RUN npm install -g @types/node@24.3.0

# Make node/npm available to all subsequent layers and at runtime
ENV PATH="$NVM_DIR/versions/node/v22.18.0/bin:$PATH"

# fix npm cache ownership (prevents EACCES when running as 'app')
RUN rm -rf /home/app/.npm && mkdir -p /home/app/.npm && chown -R app:app /home/app/.npm

### App setup #################################################################

# Switch to app user
USER app

# Set default profile to DEV
ENV PROFILE=DEV

# Add init.sh to profile.d for shell environment setup
COPY docker/init.sh /etc/profile.d/init.sh

# Copy source files
COPY src /var/app/src