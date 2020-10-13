FROM scottyhardy/docker-wine:stable-nordp

ENV WINEDEBUG -all

RUN set -ex \
    \
    && mkdir -p /home/wineuser \
    && cd /home/wineuser \
    && wget 'https://sciprogramming.com/esd_scitools/SCI_Viewer_(CLI).zip' \
    && unzip 'SCI_Viewer_(CLI).zip' \
    && rm -rf 'SCI_Viewer_(CLI).zip' 'SCI Viewer GUI'

WORKDIR /home/wineuser/SCI Viewer CLI

CMD [ "ls" ]
