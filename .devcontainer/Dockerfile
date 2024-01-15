FROM ghcr.io/aristanetworks/ansible-avd/universal:python3.11-avd-v4.5.0

ARG _ARTOKEN
ARG _AR_FILE_PATH

RUN bash -c "$(curl -sL https://get.containerlab.dev)"; \
  pip3 install anta passlib cookiecutter; \
  sudo apt-get update; sudo apt-get -y install xz-utils

RUN mkdir -p /var/tmp/

# add ceos-lab image is _ARTOKEN is not empty
RUN if [ ! -z ${_ARTOKEN} ]; \
    then \
    ARTOKEN=$(echo -n "${_ARTOKEN}" | base64) \
    && ARSESSION=$(curl -X "POST" "https://www.arista.com/custom_data/api/cvp/getSessionCode/" -H 'Content-Type: application/json'   -d "{\"accessToken\": \"$ARTOKEN\"}" | sed -n 's|.*"session_code":"\([^"]*\)".*|\1|p') \
    && FILE_PATH=${_AR_FILE_PATH} \
    && DOWNLOAD_URL=$(curl -X "POST" "https://www.arista.com/custom_data/api/cvp/getDownloadLink/" -H 'Content-Type: application/json' -d "{\"sessionCode\": \"$ARSESSION\", \"filePath\": \"$FILE_PATH\"}" | sed -n 's|.*"url":"\([^"]*\)".*|\1|p') \
    && curl "$DOWNLOAD_URL" --output /var/tmp/ceos-lab-temp \
    ; fi
