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
    libdb++-dev
```

### 3.3 Instalar MySQL

```bash
apt-get update
apt-get install -y \
    libmysqlclient-dev \
    libdbi-perl \
    libdbd-mysql-perl \
    mysql-server \
    mysql-client
```

### 3.3.1 Configurar MySQL

```bash
# Asegurarse de que el directorio de inicio del usuario mysql esté configurado
usermod -d /var/lib/mysql mysql

# Intentar iniciar MySQL nuevamente
service mysql start

# Verificar el estado
service mysql status
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

## 4. **Crear y Configurar el Espacio de Trabajo**

```bash
# Crear directorio de trabajo
mkdir -p /workspace
cd /workspace
```

## 5. **Descargar e instalar OpenCA-Tools**

```bash
# Clonar el repositorio más reciente de OpenCA-tools
git clone https://github.com/openca/openca-tools
cd openca-tools
```

```bash
# Configurar y compilar
./configure --prefix=/usr/local/openca \
    --with-openssl-prefix=/usr/lib/ssl

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
make install
```

## 7. **Configurar Variables de Entorno**

Añadir al archivo ~/.bashrc:

```bash
echo 'export PATH="/usr/local/openca/bin:$PATH"' >> ~/.bashrc
echo 'export LD_LIBRARY_PATH="/usr/local/openca/lib"' >> ~/.bashrc
source ~/.bashrc
```

## 8. **Persistir los Cambios**

Para guardar el estado del contenedor como una nueva imagen:

```bash
# Desde otra terminal (fuera del contenedor)
docker commit openca-container openca-image:1.0.0
```

## 9. **Iniciar el Contenedor**

Para futuros usos, puedes iniciar un nuevo contenedor basado en la imagen guardada:

```bash
docker run -it --name openca-new openca-image:1.0.0
```
