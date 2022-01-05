# See here for image contents: https://github.com/microsoft/vscode-dev-containers/tree/v0.209.6/containers/ubuntu/.devcontainer/base.Dockerfile

# [Choice] Ubuntu version (use hirsuite or bionic on local arm64/Apple Silicon): hirsute, focal, bionic
ARG VARIANT="focal"
FROM mcr.microsoft.com/vscode/devcontainers/base:0-${VARIANT}

# https://code.visualstudio.com/remote/advancedcontainers/add-nonroot-user
ARG USERNAME="vscode"
USER $USERNAME


# Android sdk and tools versions
ARG SDK_TOOLS_LINUX_VERSION="7583922_latest"
ARG ANDROID_PLATFORM_VERSION="29"
ARG ANDROID_BUILD_TOOLS_VERSION="30.0.2"

# Flutter version
ARG FLUTTER_CHANNEL="stable"
ARG FLUTTER_VERSION="2.8.1"


# Install additional packages
RUN sudo apt-get update \
    && export DEBIAN_FRONTEND=noninteractive \
    # Packages required for installation
    && sudo apt-get -y install --no-install-recommends openjdk-8-jdk curl unzip wget xz-utils tar \
    # Packages required for Flutter, see https://docs.flutter.dev/get-started/install/linux#system-requirements 
    && sudo apt-get -y install --no-install-recommends bash file git unzip xz-utils zip \
    # Packages required for Flutter testing, see shared libraries 
    && sudo apt-get -y install --no-install-recommends libglu1-mesa \
    && sudo apt-get clean
    # [Optional] Add packages here if needed

# Set Java env
ENV JAVA_HOME /usr/lib/jvm/java-8-openjdk-amd64


ENV HOME=/home/${USERNAME}
ENV ANDROID_SDK_ROOT=${HOME}/sdk/android-sdk-linux
ENV ANDROID_HOME=${ANDROID_SDK_ROOT}
ENV FLUTTER_HOME=${HOME}/sdk/flutter


# Install Android command line tools for sdkmanager
RUN mkdir -p ${ANDROID_SDK_ROOT}/cmdline-tools \
    && cd ${ANDROID_SDK_ROOT}/cmdline-tools \
    && wget -O sdk-tools.zip https://dl.google.com/android/repository/commandlinetools-linux-${SDK_TOOLS_LINUX_VERSION}.zip \
    && unzip sdk-tools.zip \
    && rm sdk-tools.zip \
    && mv cmdline-tools tools


# Add Android bins to the PATH 
ENV PATH=${PATH}:${ANDROID_SDK_ROOT}/cmdline-tools/latest/bin
ENV PATH=${PATH}:${ANDROID_SDK_ROOT}/cmdline-tools/tools/bin
ENV PATH=${PATH}:${ANDROID_SDK_ROOT}/platform-tools
ENV PATH=${PATH}:${ANDROID_SDK_ROOT}/emulator


# Accept the sdkmanager license
RUN cd ${ANDROID_SDK_ROOT}/cmdline-tools/tools/bin \
    && yes | sdkmanager --licenses


# Install android packages
RUN sdkmanager platform-tools \
	&& sdkmanager emulator \
	&& sdkmanager "platforms;android-$ANDROID_PLATFORM_VERSION" "build-tools;$ANDROID_BUILD_TOOLS_VERSION" "cmdline-tools;latest"


# https://docs.flutter.dev/get-started/install/linux
# Install Flutter SDK
RUN mkdir -p ${FLUTTER_HOME} \
    && cd ${FLUTTER_HOME}/.. \
	&& wget -O flutter.tar.xz https://storage.googleapis.com/flutter_infra_release/releases/${FLUTTER_CHANNEL}/linux/flutter_linux_${FLUTTER_VERSION}-${FLUTTER_CHANNEL}.tar.xz \
    && tar xf flutter.tar.xz \
	&& rm flutter.tar.xz


# Add Flutter bins to the PATH 
ENV PATH=${PATH}:${FLUTTER_HOME}/bin


# Accept the Flutter license 
RUN yes | flutter doctor --android-licenses


# [Optional] Uncomment this section and add Flutter configs here if needed
# RUN flutter config --no-analytics \
#                    --enable-web 


# Flutter update 
RUN flutter update-packages


# [Optional] Uncomment this section and add Flutter packages here if needed
# RUN flutter pub global activate <package>


# Add Flutter package bins to the PATH 
ENV PATH=${PATH}:${HOME}/sdk/flutter/.pub-cache/bin
