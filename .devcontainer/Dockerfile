FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive
ENV ANDROID_SDK_ROOT=/opt/android-sdk
ENV PATH=$ANDROID_SDK_ROOT/cmdline-tools/latest/bin:$ANDROID_SDK_ROOT/platform-tools:$PATH
ENV JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64

RUN apt-get update \
 && apt-get install -y --no-install-recommends openjdk-17-jdk wget unzip curl git ca-certificates sudo gnupg \
 && rm -rf /var/lib/apt/lists/*

RUN mkdir -p ${ANDROID_SDK_ROOT}/cmdline-tools && chown root:root ${ANDROID_SDK_ROOT}

# Download command line tools
RUN mkdir -p /tmp/cmdline && \
  cd /tmp/cmdline && \
  wget -q https://dl.google.com/android/repository/commandlinetools-linux-9477386_latest.zip -O cmdline-tools.zip && \
  unzip -q cmdline-tools.zip && \
  mkdir -p ${ANDROID_SDK_ROOT}/cmdline-tools/latest && \
  mv cmdline-tools/* ${ANDROID_SDK_ROOT}/cmdline-tools/latest/ && \
  rm -rf /tmp/cmdline

RUN yes | ${ANDROID_SDK_ROOT}/cmdline-tools/latest/bin/sdkmanager --sdk_root=${ANDROID_SDK_ROOT} --licenses || true && \
    ${ANDROID_SDK_ROOT}/cmdline-tools/latest/bin/sdkmanager --sdk_root=${ANDROID_SDK_ROOT} "platform-tools" "platforms;android-34" "build-tools;34.0.0" "cmdline-tools;latest"

ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID
RUN groupadd --gid $USER_GID $USERNAME \
 && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
 && apt-get update \
 && apt-get install -y --no-install-recommends sudo \
 && echo "$USERNAME ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/$USERNAME \
 && chmod 0440 /etc/sudoers.d/$USERNAME \
 && rm -rf /var/lib/apt/lists/*

RUN chown -R $USERNAME:$USERNAME ${ANDROID_SDK_ROOT}

USER $USERNAME
WORKDIR /home/$USERNAME

# Use gradle wrapper in repo (./gradlew). If not present, install gradle locally via SDK or use wrapper.
