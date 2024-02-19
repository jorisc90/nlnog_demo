FROM ghcr.io/aristanetworks/avd/universal:python3.11-avd-v4.6.0

ARG _ARTOKEN
ARG _AR_FILE_PATH

# install anta version that matches with current avd version https://avd.sh/en/stable/roles/eos_validate_state/ANTA-Preview.html
RUN pip3 install anta==0.12.0

# install containerlab
RUN bash -c "$(curl -sL https://get.containerlab.dev)"; \
  pip3 install passlib cookiecutter; \
  sudo apt-get update; sudo apt-get -y install xz-utils

# make build persistent tmp folder
RUN mkdir -p /var/tmp/

# add ceos-lab image if _ARTOKEN is not empty
RUN if [ ! -z ${_ARTOKEN} ]; \
    then \
    ARTOKEN=$(echo -n "${_ARTOKEN}" | base64) \
    && ARSESSION=$(curl -X "POST" "https://www.arista.com/custom_data/api/cvp/getSessionCode/" -H 'Content-Type: application/json'   -d "{\"accessToken\": \"$ARTOKEN\"}" | sed -n 's|.*"session_code":"\([^"]*\)".*|\1|p') \
    && FILE_PATH=${_AR_FILE_PATH} \
    && DOWNLOAD_URL=$(curl -X "POST" "https://www.arista.com/custom_data/api/cvp/getDownloadLink/" -H 'Content-Type: application/json' -d "{\"sessionCode\": \"$ARSESSION\", \"filePath\": \"$FILE_PATH\"}" | sed -n 's|.*"url":"\([^"]*\)".*|\1|p') \
    && curl "$DOWNLOAD_URL" --output /var/tmp/ceos-lab-temp \
    ; fi
