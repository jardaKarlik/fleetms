# syntax=docker/dockerfile:1.4

ARG ELIXIR_VERSION=1.17.2
ARG OTP_VERSION=27.0
ARG UBUNTU_VERSION=jammy-20240530

ARG IMAGE="hexpm/elixir:${ELIXIR_VERSION}-erlang-${OTP_VERSION}-ubuntu-${UBUNTU_VERSION}"
FROM ${IMAGE}

# This Dockerfile adds a non-root user with sudo access
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Settings for Phoenix
ARG PHOENIX_VERSION="1.7.14"
ARG NODE_VERSION="20"

# Install needed packages and setup non-root user
RUN apt-get update \
  && export DEBIAN_FRONTEND=noninteractive \
  && apt-get -y install --no-install-recommends \
     curl \
     ca-certificates \
     gnupg2 \
     dirmngr \
     gpg \
     gawk \
     build-essential \
     postgresql-client \
     inotify-tools \
     imagemagick \
     sudo \
  # Install Node.js via NodeSource
  && curl -fsSL https://deb.nodesource.com/setup_${NODE_VERSION}.x | bash - \
  && apt-get install -y nodejs \
  # Create non-root user
  && groupadd --gid ${USER_GID} ${USERNAME} \
  && useradd --uid ${USER_UID} --gid ${USER_GID} -m ${USERNAME} \
  && echo "${USERNAME} ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/${USERNAME} \
  && chmod 0440 /etc/sudoers.d/${USERNAME} \
  # Clean up
  && apt-get autoremove -y \
  && apt-get clean -y \
  && rm -rf /var/lib/apt/lists/*

# Install Hex, Rebar, and Phoenix
RUN mix local.hex --force \
  && mix local.rebar --force \
  && mix archive.install --force hex phx_new ${PHOENIX_VERSION}

ENV MIX_ENV=dev
