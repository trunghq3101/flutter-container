ARG VARIANT=focal
FROM mcr.microsoft.com/vscode/devcontainers/base:${VARIANT}

ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

ARG ENABLE_ANDROID_SDK=false

ENV ANDROID_SDK_ROOT="/home/$USERNAME/android"
ENV FLUTTER_CHANNEL="stable"
ENV FLUTTER_VERSION="2.8.0"
ENV FLUTTER_URL="https://storage.googleapis.com/flutter_infra_release/releases/$FLUTTER_CHANNEL/linux/flutter_linux_$FLUTTER_VERSION-$FLUTTER_CHANNEL.tar.xz"
ENV FLUTTER_HOME="/home/$USERNAME/flutter"
ENV FLUTTER_WEB_PORT="8090"
ENV FLUTTER_DEBUG_PORT="42000"
ENV FLUTTER_EMULATOR_NAME="flutter_emulator"
ENV PATH="$ANDROID_SDK_ROOT/cmdline-tools/latest/bin:$ANDROID_SDK_ROOT/cmdline-tools/latest:$ANDROID_SDK_ROOT/emulator:$ANDROID_SDK_ROOT/platform-tools:$ANDROID_SDK_ROOT/platforms:$FLUTTER_HOME/bin:$PATH"

ENV DEBIAN_FRONTEND="noninteractive"
RUN apt-get update && apt-get install --yes --no-install-recommends xz-utils libgl1-mesa-glx

USER $USERNAME

# flutter
RUN mkdir -p $FLUTTER_HOME \
  && cd $FLUTTER_HOME \
  && curl -o flutter.tar.xz $FLUTTER_URL \
  && tar xf flutter.tar.xz -C /home/$USERNAME \
  && rm flutter.tar.xz 
RUN flutter config --no-analytics \
  && flutter precache 

RUN if [ "${ENABLE_ANDROID_SDK}" = "true" ]; then \
  yes "y" | flutter doctor --android-licenses \
  && flutter doctor \
  && flutter update-packages; \
  fi

COPY flutter-web.sh /usr/local/bin/flutter-web
RUN sudo chown $USERNAME:$USERNAME /usr/local/bin/flutter-web

ENV PROMPT_COMMAND="history -a"
ENV HISTFILE="/home/$USERNAME/commandhistory/.zsh_history"
RUN mkdir -p /home/$USERNAME/commandhistory \
  && touch /home/$USERNAME/commandhistory/.zsh_history 

ENV JAVA_HOME="/home/$USERNAME/java-home"

ENTRYPOINT [ "/usr/local/bin/flutter-web.sh" ]
