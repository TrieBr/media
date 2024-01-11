FROM ubuntu AS base
EXPOSE 6789
RUN apt-get update
RUN apt-get install -y wget
RUN wget https://nzbget.net/download/nzbget-latest-bin-linux.run
RUN sh nzbget-latest-bin-linux.run --destdir /app
RUN echo $USENET_HOST
WORKDIR /nzbget
ENTRYPOINT /app/nzbget -s -o outputmode=log -c /config/nzbget-config.conf
