# NEO csharp-nodes network simulator
FROM ubuntu:18.04

ENV DEBIAN_FRONTEND noninteractive

# Disable dotnet usage information collection
# https://docs.microsoft.com/en-us/dotnet/core/tools/telemetry#behavior
ENV DOTNET_CLI_TELEMETRY_OPTOUT 1

# Install system dependencies. always should be done in one line
# https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/#run
RUN apt-get update && apt-get install -y \
    unzip \
    screen \
    expect \
    libleveldb-dev \
    git-core \
    wget \
    curl \
    vim \
    nano \
    cron \
    man

RUN apt-get install -y gnupg2 gnupg gnupg1
RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
RUN echo "deb https://download.mono-project.com/repo/ubuntu stable-bionic main" | tee /etc/apt/sources.list.d/mono-official-stable.list
RUN wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.asc.gpg
RUN mv microsoft.asc.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
RUN wget -q https://packages.microsoft.com/config/ubuntu/18.04/prod.list
RUN mv prod.list /etc/apt/sources.list.d/microsoft-prod.list
RUN apt-get update && apt-get install -y apt-transport-https dotnet-sdk-2.1.200

RUN apt-get update && apt-get install -y iputils-ping net-tools # we need ping and netstat, come on...

# APT cleanup to reduce image size
RUN rm -rf /var/lib/apt/lists/*

# Add the neo-cli package
ADD ./neo-cli.zip /opt/neo-cli.zip

# Extract and prepare four consensus nodes
RUN unzip -q -d /opt/node /opt/neo-cli.zip


# Add config files
ADD ./configs/protocol.json /opt/node/neo-cli/protocol.json

ADD ./configs/config1.json /opt/node/neo-cli/config1.json
ADD ./wallets/wallet1.json /opt/node/neo-cli/

ADD ./configs/config2.json /opt/node/neo-cli/config2.json
ADD ./wallets/wallet2.json /opt/node/neo-cli/

ADD ./configs/config3.json /opt/node/neo-cli/config3.json
ADD ./wallets/wallet3.json /opt/node/neo-cli/

ADD ./configs/config4.json /opt/node/neo-cli/config4.json
ADD ./wallets/wallet4.json /opt/node/neo-cli/

ADD ./configs/config1RPC.json /opt/node/neo-cli/config1RPC.json
ADD ./configs/config2RPC.json /opt/node/neo-cli/config2RPC.json

#Script used to start nodes on screen sessions
ADD ./scripts/run.sh /opt/

#Script used to open a wallet and start a neo-cli consensus
ADD ./scripts/start_consensus_node.sh /opt/
ADD ./scripts/start_rpc_node.sh /opt/

#Script used to modify neo-csharp-nodes characteristcs and parameters
ADD ./scripts/updateConsensusCharacteristics.sh /opt/

# On docker run, start the consensus nodes
CMD ["/bin/bash", "/opt/run.sh"]