FROM fedora:21

MAINTAINER Felix Massem <felix.massem@codecentric.de>, Olaf Goerlitz <olaf.goerlitz@gmail.com>

# Install Java
RUN yum -y -q update
RUN yum -y -q install jre

# Install ElasticSearch
ADD elasticsearch.repo /etc/yum.repos.d/
RUN \
  rpm --import https://packages.elastic.co/GPG-KEY-elasticsearch && \
  yum install -y -q elasticsearch \
  yum install -y python-pip

# Link configuration files and set /data and /logs directories
RUN \
  ln -s /etc/elasticsearch /usr/share/elasticsearch/config && \
  sed -ri 's/#path.(data|logs):[^,]*$/path.\1: \/\1/' /etc/elasticsearch/elasticsearch.yml && \
  cd /usr/share/elasticsearch/bin/ && \
  ./plugin install mobz/elasticsearch-head && \
  pip install elasticsearch-curator && \
  mkdir -p /usr/share/elasticsearch/logs && \
  mkdir /snapshot && \
  chown -R elasticsearch:elasticsearch /snapshot && \
  chown -R elasticsearch:elasticsearch /usr/share/elasticsearch && \
  chown -R elasticsearch:elasticsearch /var/log/elasticsearch && \
  chown -R elasticsearch:elasticsearch /var/lib/elasticsearch

ADD ./elasticsearch.yml /etc/elasticsearch/elasticsearch.yml
# Define data volumes for index data and log files
VOLUME [ "/data", "/logs" ]

# Elasticsearch should usually be started as a service via 'service elasticsearch start'.
# However, there are no services running in the container. Hence, we start Elasticsearch directly.
USER elasticsearch
CMD [ "/usr/share/elasticsearch/bin/elasticsearch" ]

# Expose HTTP service port only
EXPOSE 9200
