FROM ubuntu:19.10 AS builder-rocksdb-tools
RUN set -x \
 && apt update > /dev/null \
 && apt install -y git cmake g++ libgflags-dev libsnappy-dev zlib1g-dev libbz2-dev liblz4-dev libzstd-dev > /dev/null \
 && cd /root \
 && git clone -b v6.6.3 https://github.com/facebook/rocksdb.git > /dev/null \
 && mkdir -p /root/rocksdb/build \
 && cd /root/rocksdb/build \
 && cmake .. > /dev/null \
 && cd /root/rocksdb/build/tools \
 && make ldb sst_dump -j 2 > /dev/null \
 && mkdir -p /root/rocksdb-tools \
 && cp -p /root/rocksdb/build/tools/ldb /root/rocksdb-tools/ \
 && cp -p /root/rocksdb/build/tools/sst_dump /root/rocksdb-tools/

FROM ubuntu:19.10
RUN set -x \
 && apt update > /dev/null \
 && apt install -y libgflags2.2 > /dev/null \
 && apt-get -y clean > /dev/null \
 && rm -rf /var/lib/apt/lists/* \
 && mkdir -p /root/rocksdb/build 
COPY --from=builder-rocksdb-tools /root/rocksdb/build/librocksdb.so.6.6.3 /root/rocksdb/build/librocksdb.so.6
COPY --from=builder-rocksdb-tools /root/rocksdb-tools/* /usr/local/bin/
CMD ["/bin/bash"]
