FROM multiarch/qemu-user-static:x86_64-aarch64 as qemu
FROM jellyfin/jellyfin as jellyfin
FROM metricvoid/jetson-ffmpeg:r20211015 as app

# https://askubuntu.com/questions/972516/debian-frontend-environment-variable
ARG DEBIAN_FRONTEND="noninteractive"
# http://stackoverflow.com/questions/48162574/ddg#49462622
ARG APT_KEY_DONT_WARN_ON_DANGEROUS_USAGE=DontWarn
# https://github.com/NVIDIA/nvidia-docker/wiki/Installation-(Native-GPU-Support)
ENV NVIDIA_DRIVER_CAPABILITIES="compute,video,utility"

COPY --from=qemu /usr/bin/qemu-aarch64-static /usr/bin

# curl: healcheck
RUN apt-get update && apt-get install --no-install-recommends --no-install-suggests -y \
 libssl-dev \
 ca-certificates \
 libfontconfig1 \
 libfreetype6 \
 libomxil-bellagio0 \
 libomxil-bellagio-bin \
 locales \
 curl \
 && apt-get clean autoclean -y \
 && apt-get autoremove -y \
 && rm -rf /var/lib/apt/lists/* \
 && mkdir -p /cache /config /media \
 && chmod 777 /cache /config /media \
 && sed -i -e 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen && locale-gen

# ENV DOTNET_SYSTEM_GLOBALIZATION_INVARIANT=1
ENV LC_ALL en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en

FROM app

ENV HEALTHCHECK_URL=http://localhost:8096/health

COPY --from=jellyfin /jellyfin /jellyfin
COPY --from=jellyfin /jellyfin/jellyfin-web /jellyfin/jellyfin-web

EXPOSE 8096
VOLUME /cache /config
ENTRYPOINT ["./jellyfin/jellyfin", \
    "--datadir", "/config", \
    "--cachedir", "/cache", \
    "--ffmpeg", "ffmpeg"]

HEALTHCHECK --interval=30s --timeout=30s --start-period=10s --retries=3 \
     CMD curl -Lk -fsS "${HEALTHCHECK_URL}" || exit 1
     
#sudo sudo docker run -it -v /usr/lib/aarch64-linux-gnu/tegra:/usr/lib/aarch64-linux-gnu/tegra -v $(pwd):/media -p 8096:8096 --runtime=nvidia --entrypoint bash 78a901c87319

#ffmpeg -i /media/sample_3840x2160.mkv -c:v hevc_nvmpi -b:v 50M /media/sample_3840x2160.mp4