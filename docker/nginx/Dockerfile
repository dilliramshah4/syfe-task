FROM ubuntu:20.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    build-essential \
    libpcre3-dev \
    libssl-dev \
    zlib1g-dev \
    libgeoip-dev \
    wget \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /tmp

RUN wget https://openresty.org/download/openresty-1.21.4.1.tar.gz \
    && tar -xzf openresty-1.21.4.1.tar.gz \
    && cd openresty-1.21.4.1 \
    && ./configure \
        --with-luajit \
        --with-http_ssl_module \
        --with-http_realip_module \
        --with-http_geoip_module \
        --with-http_stub_status_module \
    && make -j$(nproc) \
    && make install

RUN ln -sf /usr/local/openresty/nginx/sbin/nginx /usr/local/bin/nginx

# Create log directory
RUN mkdir -p /var/log/nginx

COPY nginx.conf /usr/local/openresty/nginx/conf/nginx.conf

EXPOSE 80

CMD ["/usr/local/openresty/nginx/sbin/nginx", "-g", "daemon off;"]
