FROM alpine:latest
LABEL maintainer="Steven Goossens"
LABEL description="Timesketch Docker image based on Alpine"

ENV LANG en_US.UTF-8
ENV LC_ALL en_US.UTF-8
ENV CFLAGS="$CFLAGS -D_GNU_SOURCE"

# Update the base image
RUN apk update && apk upgrade

# Install dependencies
RUN apk add --no-cache wget \
             sudo \
             nano \
             curl \
             bash \
             openssl \
             python3 \
             python3-dev \
             py3-pip \
             py3-setuptools \
             bash \
             openssl-dev \
             xz-dev \
             zeromq \
             libffi-dev \
             py3-lz4 \
             py3-openssl \
             py3-numpy \
             py3-cryptography \
             postgresql-dev \
             build-base \
             git \
             linux-headers

ARG TZ=Europe/Amsterdam
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apk add curl wget sudo tzdata && adduser -D plaso

# Use Python 3 pip (pip3) to install Timesketch
RUN pip3 install --trusted-host pypi.python.org --trusted-host pypi.org --trusted-host files.pythonhosted.org psycopg2-binary
RUN git config --global http.sslVerify false
RUN git clone https://github.com/google/timesketch.git
RUN cd timesketch && pip3 install -r requirements.txt && python3 setup.py install 

RUN git clone --branch=master \
               --depth=1 \
               https://github.com/log2timeline/plaso.git \
               /plaso \
  && cd /plaso \
  && pip3 install --upgrade pip \
  && pip3 install  -r requirements.txt \
  && pip3 install  elasticsearch\
  && python3 setup.py install


# Copy Timesketch config files into /etc/timesketch
ADD . /tmp/timesketch
RUN mkdir /etc/timesketch

# Copy the entrypoint script into the container
COPY docker-entrypoint.sh /
RUN chmod a+x /docker-entrypoint.sh

# Expose the port used by Timesketch
EXPOSE 5000

# Load the entrypoint script to be run later
ENTRYPOINT ["/docker-entrypoint.sh"]

# Invoke the entrypoint script
CMD ["timesketch"]
