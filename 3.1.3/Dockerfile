FROM debian:buster-slim

RUN set -eux; \
	apt-get update; \
	apt-get install -y --no-install-recommends \
		bzip2 \
		ca-certificates \
		libffi-dev \
		libgmp-dev \
		libssl-dev \
		libyaml-dev \
		procps \
		zlib1g-dev \
    gnupg2 \
    curl \
    git \
		libreadline-dev \
    build-essential \
		wget \
    less \
    lsb-release \
    shared-mime-info \
		openssh-client \
	; \
	rm -rf /var/lib/apt/lists/*

# ensure we use bash for all RUN commands
# use -l to use interactive login shell
# and ensure modifications to bashrc are properly sourced
SHELL ["/bin/bash", "-lc"]

ENV LANG C.UTF-8
ENV TMPDIR "$HOME/tmp"

RUN git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.10.2 && \
    echo ". $HOME/.asdf/asdf.sh" >> /root/.bashrc && \
    echo ". $HOME/.asdf/asdf.sh" >> /root/.zshrc

RUN asdf plugin add ruby && \
		asdf install ruby 3.1.3 && \
		asdf global ruby 3.1.3 && \
		gem update --system && \
    gem install bundler:2.3.26 && \
    bundle config ssl_verify_mode 0 && \
    echo ":ssl_verify_mode: 0" > ~/.gemrc

# Add NodeJS to sources list
RUN curl -fsSL https://deb.nodesource.com/gpgkey/nodesource.gpg.key | apt-key add -
RUN curl -sL https://deb.nodesource.com/setup_14.x | bash -

# Add PostgreSQL to sources list
RUN wget -q -O - https://dl.google.com/linux/linux_signing_key.pub |  apt-key add -
RUN curl https://www.postgresql.org/media/keys/ACCC4CF8.asc | gpg --dearmor | tee /etc/apt/trusted.gpg.d/apt.postgresql.org.gpg >/dev/null
RUN sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt buster-pgdg main" > /etc/apt/sources.list.d/pgdg.list'

# Install dependencies
RUN apt-get update -qq && \
  DEBIAN_FRONTEND=noninteractive apt-get install -yq \
    libpq-dev \
    postgresql-client-14 \
		postgresql-contrib-14 \
    nodejs && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    truncate -s 0 /var/log/*log

RUN npm install -g yarn@1.22.19

# set all the things to be sure bash is always used as the default shell
RUN cd /bin && ln -sf bash sh && chsh -s /bin/bash
ENV SHELL=bash
ENV PATH=$PATH:/root/.bin
WORKDIR /root
ENTRYPOINT [ "/bin/bash", "-lc" ]
