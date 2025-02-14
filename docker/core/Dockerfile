FROM ubuntu:18.04 as sample

RUN apt-get update && apt-get install -y \
    vim \
    autoconf \
    automake \
    build-essential \
    cmake \
    curl \
    debhelper \
    git \
    libcurl4-openssl-dev \
    libprotobuf-dev \
    libssl-dev \
    libtool \
    lsb-release \
    ocaml \
    ocamlbuild \
    protobuf-compiler \
    python \
    wget \
    libcurl4 \
    libprotobuf10 \
    libssl1.1 \
    make \
    module-init-tools \
    g++ \
    libjsoncpp-dev\
    uuid-dev

# Install the SDK
WORKDIR /opt/intel
RUN wget https://download.01.org/intel-sgx/sgx-dcap/1.12.1/linux/distro/ubuntu18.04-server/sgx_linux_x64_sdk_2.15.101.1.bin \
    && chmod 777 sgx_linux_x64_sdk_2.15.101.1.bin \
    && sh -c 'echo yes | ./sgx_linux_x64_sdk_2.15.101.1.bin'

# Install DCAP packages
# DCAP repository setup
RUN echo 'deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main' | tee /etc/apt/sources.list.d/intel-sgx.list \
    && wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | apt-key add -

RUN apt-get update &&apt-get install -y \
    libsgx-enclave-common-dev \
    libsgx-ae-qe3 \
    libsgx-ae-qve \
    libsgx-urts \
    libsgx-dcap-ql \
    libsgx-dcap-default-qpl \
    libsgx-dcap-quote-verify-dev \
    libsgx-dcap-default-qpl-dev \
    libsgx-quote-ex-dev  \
    libsgx-uae-service \
    libsgx-ra-network \
    libsgx-ra-uefi \
    libsgx-dcap-ql-dev

COPY start_ehsm-kms.sh /home/
RUN chmod 744 /home/start_ehsm-kms.sh

# Build App from source
WORKDIR /home
RUN git clone https://github.com/intel/ehsm.git \
    && cd ehsm \
    && make

# Install node & npm packages
RUN wget https://nodejs.org/dist/v16.13.0/node-v16.13.0-linux-x64.tar.xz \
    && tar xf  node-v16.13.0-linux-x64.tar.xz \
    && mv node-v16.13.0-linux-x64/ /usr/local/nodejs \
    && ln -s /usr/local/nodejs/bin/node /usr/local/bin \
    && ln -s /usr/local/nodejs/bin/npm /usr/local/bin


# Install ehsm-service dependence packages
WORKDIR /home/ehsm/ehsm_kms_service
RUN cp /home/ehsm/out/ehsm-core/libehsmnapi.so . \
    && cp /home/ehsm/out/ehsm-core/libenclave-ehsm-core.signed.so .
RUN npm install

# image port
EXPOSE 9000

# run ehsm_kms_service
CMD ["sh", "/home/start_ehsm-kms.sh"]


#RUN adduser -q --disabled-password --gecos "" --no-create-home sgxuser
#USER sgxuser

# CMD tail -f /dev/null