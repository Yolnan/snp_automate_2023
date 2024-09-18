ARG TAG
FROM ghcr.io/ros-industrial-consortium/scan_n_plan_workshop:${TAG}

SHELL ["/bin/bash", "-c"]

ENV DEBIAN_FRONTEND noninteractivej

USER root

# Run apt update and install useful utilities like sudo
RUN apt update -y -qq && apt install -y -qq sudo

# Install numpy 1.26
RUN python3 -m pip install numpy==1.26

# Install pyquaternion
RUN python3 -m pip install pyquaternion --upgrade --upgrade-strategy only-if-needed 

# Install open3d
RUN python3 -m pip install open3d==0.18.0 --upgrade --upgrade-strategy only-if-needed 

# Bind mount the source directory so as not to unnecessarily copy source code into the docker image
ARG WORKSPACE_DIR=/opt/snp_automate_2023
RUN --mount=type=bind,target=${WORKSPACE_DIR}/src/snp_automate_2023 \
  apt update -y -qq \
  && source /opt/snp/install/setup.bash \
  && vcs import ${WORKSPACE_DIR}/src < ${WORKSPACE_DIR}/src/snp_automate_2023/dependencies.repos --shallow \
  && rosdep install \
    --from-paths ${WORKSPACE_DIR}/src \
    -iry

# Build the repository
# Bind mount the source directory so as not to unnecessarily copy source code into the docker image
RUN --mount=type=bind,target=${WORKSPACE_DIR}/src/snp_automate_2023 \
  source /opt/snp/install/setup.bash \
  && cd ${WORKSPACE_DIR} \ 
  && colcon build --cmake-args \
  && rm -rf build log

COPY --chmod=755 docker/launch_snp_automate_2023.sh /opt/launch_snp_automate_2023.sh
ENTRYPOINT ["/opt/launch_snp_automate_2023.sh"]
