FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

# =========================
# 基本ツール
# =========================
RUN apt-get update && apt-get install -y \
    git \
    curl \
    unzip \
    zip \
    build-essential \
    python3 \
    python3-pip \
    wget \
    ninja-build \
    openjdk-11-jdk \
    && rm -rf /var/lib/apt/lists/*

RUN pip3 install --upgrade pip
RUN pip3 install numpy packaging

# =========================
# Java 11固定
# =========================
ENV JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
ENV PATH=$JAVA_HOME/bin:$PATH

# =========================
# Gradle 6.9（ORT 1.14互換）
# =========================
RUN wget https://services.gradle.org/distributions/gradle-6.9.4-bin.zip -O gradle.zip && \
    unzip gradle.zip -d /opt && \
    mv /opt/gradle-6.9.4 /opt/gradle && \
    rm gradle.zip

ENV PATH=/opt/gradle/bin:$PATH

# =========================
# CMake（ORT 1.14互換の安全版）
# =========================
RUN wget https://github.com/Kitware/CMake/releases/download/v3.27.9/cmake-3.27.9-linux-x86_64.sh && \
    chmod +x cmake-3.27.9-linux-x86_64.sh && \
    ./cmake-3.27.9-linux-x86_64.sh --skip-license --prefix=/usr/local && \
    rm cmake-3.27.9-linux-x86_64.sh

# =========================
# Android SDK
# =========================
ENV ANDROID_SDK_ROOT=/opt/android-sdk

RUN mkdir -p /opt/android-sdk/cmdline-tools && \
    cd /opt/android-sdk/cmdline-tools && \
    wget https://dl.google.com/android/repository/commandlinetools-linux-9477386_latest.zip && \
    unzip commandlinetools-linux-*.zip && \
    mv cmdline-tools latest && \
    rm commandlinetools-linux-*.zip

ENV PATH=$ANDROID_SDK_ROOT/cmdline-tools/latest/bin:$ANDROID_SDK_ROOT/platform-tools:$PATH

RUN yes | sdkmanager --licenses && \
    sdkmanager \
      "platform-tools" \
      "platforms;android-21" \
      "build-tools;30.0.3"

# =========================
# NDK（重要：r25cにダウングレード）
# =========================
ENV ANDROID_NDK_VERSION=r25c
ENV ANDROID_NDK_ROOT=/opt/android-ndk

RUN mkdir -p /opt && cd /opt && \
    wget https://dl.google.com/android/repository/android-ndk-${ANDROID_NDK_VERSION}-linux.zip && \
    unzip android-ndk-${ANDROID_NDK_VERSION}-linux.zip && \
    mv android-ndk-${ANDROID_NDK_VERSION} android-ndk && \
    rm android-ndk-${ANDROID_NDK_VERSION}-linux.zip

ENV PATH=$ANDROID_NDK_ROOT:$PATH

# =========================
# ORTビルド安定用
# =========================
ENV GRADLE_OPTS="-Dorg.gradle.daemon=false"

ENV LDFLAGS="-Wl,-z,max-page-size=16384 -Wl,-z,common-page-size=16384"
ENV CFLAGS="-fPIC"
ENV CXXFLAGS="-fPIC"

WORKDIR /workspace

CMD ["/bin/bash"]
