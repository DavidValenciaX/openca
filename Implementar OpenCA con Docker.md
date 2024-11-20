# Crear un contenedor de Docker que actúe como un sistema operativo Linux para implementar OpenCA. Guía paso a paso

## 1. **Crear el Contenedor Base**

Comenzaremos con Ubuntu 16.04, que es la versión más reciente y estable:

```bash
# Detener el contenedor si está corriendo
docker stop openca-container

# Eliminar el contenedor si existe
docker rm openca-container

# Crear un nuevo contenedor
docker run -it --name openca-container ubuntu:16.04
```

## 3. **Actualizar e Instalar Dependencias Básicas**

```bash
# Actualizar el sistema en general
apt-get update
apt-get upgrade --fix-missing
```

### 3.1 Instalar herramientas esenciales y dependencias

```bash
apt-get install -y \
    build-essential checkinstall \
    curl wget git gnupg nano vim htop unzip \
    unzip automake autoconf \
    libxml2-dev virtualenv python3 python3-pip python3-tabulate \
    cmake ninja-build python3-pytest \
    python3-jinja2 python3-yaml apt-utils \
    apt-transport-https uuid-dev \
    git-crypt git-repair git-review \
    netcat-traditional arping 2ping ssh \
    gcc gdb lldb clang ssh libreadline-dev zlib1g-dev
```

### 3.2 Instalar Berkeley DB y módulos Perl necesarios

```bash
apt-get install -y \
    build-essential \
    autoconf \
    automake \
    libtool \
    libltdl-dev \
    gettext \
    pkg-config \
    libperl-dev \
    libssl-dev \
    libssl-doc \
    libsslcommon2-dev \
    libdbd-pg-perl \
    libnet-ssleay-perl \
    libcgi-session-perl \
    libxml-libxml-perl \
    libxml-parser-perl \
    libapache2-mod-perl2 \
    cpanminus \
    libdb-dev \
    libdb++-dev \
    libauthen-sasl-perl \
    libconvert-asn1-perl \
    libdigest-hmac-perl \
    libdigest-sha-perl \
    libencode-locale-perl \
    libio-socket-ssl-perl \
    libmime-base64-perl \
    libmime-lite-perl \
    libmime-tools-perl \
    libnet-ldap-perl \
    libparse-recdescent-perl \
    liburi-perl \
    libxml-twig-perl \
    libintl-perl \
    libdbi-perl
```

### 3.4 Instalar Postgresql

```bash
apt-get install -y \
    postgresql \
    postgresql-server-dev-all \
    libpq-dev \
    libdbd-pg-perl
```

### 3.5 Escoger opciones de postgresql

```bash
# Escoger opciones de postgresql
2. para america
29. para Timezone Bogota
```

## 5. **Descargar e instalar OpenCA-Tools**

```bash
# Clonar el repositorio más reciente de OpenCA-tools
git clone https://github.com/openca/openca-tools
cd openca-tools
```

```bash
# Configurar y compilar
./configure

# Compilar e instalar
make
make install

# Volver al directorio anterior
cd ..
```

## 6. **Instalar OpenCA**

```bash
# Clonar el repositorio de OpenCA
git clone https://github.com/openca/openca-base.git
cd openca-base

# Configurar OpenCA
./configure --prefix=/usr/local/openca \
    --with-httpd-user=www-data \
    --with-httpd-group=www-data \
    --with-web-host=localhost \
    --with-ca-organization="Universidad Surcolombiana" \
    --with-ca-country=CO \
    --with-ca-state="Huila" \
    --with-ca-locality="Neiva"

# Compilar e instalar
make
make install-offline
make install-online
```
