FROM php:5.6-apache

ENV LD_LIBRARY_PATH="/opt/oracle/instantclient_12_1"
ENV OCI_HOME="/opt/oracle/instantclient_12_1"
ENV OCI_LIB_DIR="/opt/oracle/instantclient_12_1"
ENV OCI_INCLUDE_DIR="/opt/oracle/instantclient_12_1/sdk/include"
ENV OCI_VERSION=12

    # Install wget
    RUN apt-get update && apt-get install --no-install-recommends -y wget unzip
    # Install Oracle Instantclient
    RUN mkdir /opt/oracle 
    RUN cd /opt/oracle \
        && wget https://github.com/diogomascarenha/oracle-instantclient/raw/master/instantclient-basic-linux.x64-12.1.0.2.0.zip \
        && wget https://github.com/diogomascarenha/oracle-instantclient/raw/master/instantclient-sdk-linux.x64-12.1.0.2.0.zip \
        && unzip /opt/oracle/instantclient-basic-linux.x64-12.1.0.2.0.zip -d /opt/oracle \
        && unzip /opt/oracle/instantclient-sdk-linux.x64-12.1.0.2.0.zip -d /opt/oracle \
        && ln -s /opt/oracle/instantclient_12_1/libclntsh.so.12.1 /opt/oracle/instantclient_12_1/libclntsh.so \
        && ln -s /opt/oracle/instantclient_12_1/libclntshcore.so.12.1 /opt/oracle/instantclient_12_1/libclntshcore.so \
        && ln -s /opt/oracle/instantclient_12_1/libocci.so.12.1 /opt/oracle/instantclient_12_1/libocci.so \
        && rm -rf /opt/oracle/*.zip 
    # Install PHP extensions deps
    RUN apt-get update \
        && apt-get install --no-install-recommends -y \
            libaio-dev \
            freetds-dev \
            libldap2-dev

    RUN ln -s /usr/lib/x86_64-linux-gnu/libldap.so /usr/lib/libldap.so \
        && ln -s /usr/lib/x86_64-linux-gnu/liblber.so /usr/lib/liblber.so

    # Install PHP extensions

    RUN echo 'instantclient,/opt/oracle/instantclient_12_1/' | pecl install oci8-2.0.10;
    RUN docker-php-ext-configure pdo_oci --with-pdo-oci=instantclient,/opt/oracle/instantclient_12_1,12.1 
    RUN docker-php-ext-configure pdo_dblib --with-libdir=/lib/x86_64-linux-gnu 
    RUN docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu
    RUN docker-php-ext-install \
                pdo \
                pdo_oci \
                pdo_mysql \
        && docker-php-ext-enable \
                oci8 \
        && docker-php-ext-install ldap 

    RUN a2enmod rewrite 
    RUN a2enmod authnz_ldap
    RUN a2enmod ssl

    EXPOSE 80
    EXPOSE 443