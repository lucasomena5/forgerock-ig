#
#  Copyright 2020-2022 ForgeRock AS. All Rights Reserved
#
#  Use of this code requires a commercial software license with ForgeRock AS.
#  or with one of its affiliates. All use shall be exclusively subject
#  to such license between the licensee and ForgeRock AS.
#
# =============================================================================================================
#  ForgeRock provides no commercial support for production deployments that use ForgeRock’s evaluation-only
#  Docker images.
#  When deploying the ForgeRock Identity Platform using Docker images, you must build and use your own images
#  for production deployments.
# =============================================================================================================
#
FROM gcr.io/forgerock-io/java-11:latest
#
# Default install directory for IG. Override this to set a different location.
#
ENV INSTALL_DIR /opt/ig

COPY --chown=forgerock:root . "${INSTALL_DIR}"
#
# Default home for IG config. Override this to set a different location.
#
ENV IG_INSTANCE_DIR /var/ig
ENV IG_INSTANCE_SECRETS_DIR /var/ig/secrets

# SECRETS
ENV HEALTH_URI_PATH health
ENV HEAP_CONFIG_AGENT_PASSWORD password

RUN mkdir -p "${IG_INSTANCE_DIR}" \
    && chown -R forgerock:root "${IG_INSTANCE_DIR}" "${INSTALL_DIR}" \
    && chmod -R g+rwx "${IG_INSTANCE_DIR}" "${INSTALL_DIR}"

COPY ./config/health.json "$IG_INSTANCE_DIR"/config/health.json
COPY ./config/admin.json "$IG_INSTANCE_DIR"/config/admin.json
COPY ./config/config.json "$IG_INSTANCE_DIR"/config/config.json

COPY ./config/routes/static-resources.json "$IG_INSTANCE_DIR"/config/routes/static-resources.json
COPY ./config/routes/jwt-idc.json "$IG_INSTANCE_DIR"/config/routes/jwt-idc.json

RUN apt-get -y update; apt-get install -y curl

USER 11111

ENTRYPOINT ${INSTALL_DIR}/bin/start.sh ${IG_INSTANCE_DIR}
