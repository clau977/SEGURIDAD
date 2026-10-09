FROM debian:bookworm

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    krb5-user ldap-utils ca-certificates \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

ENV TLS_CACERT=/pki/ca/ca.crt
RUN echo "TLS_CACERT /pki/ca/ca.crt" >> /etc/ldap/ldap.conf

CMD ["sleep", "infinity"]
