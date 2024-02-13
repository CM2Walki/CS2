###########################################################
# Dockerfile that builds a CS2 Gameserver
###########################################################
FROM cm2network/steamcmd:root as build_stage

LABEL maintainer="walentinlamonos@gmail.com"

ENV STEAMAPPID 730
ENV STEAMAPP cs2
ENV STEAMAPPDIR "${HOMEDIR}/${STEAMAPP}-dedicated"

COPY etc/entry.sh "${HOMEDIR}/entry.sh"
COPY etc/server.cfg "${STEAMAPPDIR}/game/csgo/cfg/server.cfg"

RUN set -x \
	# Install, update & upgrade packages
	&& apt-get update \
	&& apt-get install -y --no-install-recommends --no-install-suggests \
		wget=1.21.3-1+b2 \
		ca-certificates=20230311 \
		lib32z1=1:1.2.13.dfsg-1 \
	&& mkdir -p "${STEAMAPPDIR}" \
	# Add entry script
	&& chmod +x "${HOMEDIR}/entry.sh" \
	&& chown -R "${USER}:${USER}" "${HOMEDIR}/entry.sh" "${STEAMAPPDIR}" \
	# Clean up
	&& rm -rf /var/lib/apt/lists/* 

FROM build_stage AS bookworm-base

ENV CS2_SERVERNAME="New \"${STEAMAPP}\" Server" \
    CS2_PORT=27015 \
    CS2_MAXPLAYERS=10 \
    CS2_RCONPW="changeme" \
    CS2_PW="changeme" \
    CS2_MAPGROUP="mg_active" \    
    CS2_STARTMAP="de_dust2" \
    CS2_GAMETYPE=0 \
    CS2_GAMEMODE=1 \
    CS2_LAN=0 \
    CS2_ADDITIONAL_ARGS=""

# Switch to user
USER ${USER}

WORKDIR ${HOMEDIR}

CMD ["bash", "entry.sh"]

# 27015/udp = game traffic
# 27015/tcp = rcon traffic
EXPOSE 27015/tcp \
	27015/udp
