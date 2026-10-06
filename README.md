# price-tracker-automationname: Build EasyEUICC debug APK

on:
  workflow_dispatch:
  push:
    branches: [ main, master ]

jobs:
  build:
    runs-on: ubuntu-latest
    timeout-minutes: 60
    steps:
      - name: Checkout (with submodules)
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0   # versioning plugin uses git describe / rev-list

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'

      - name: Accept Android SDK licenses
        run: yes | "$ANDROID_HOME/cmdline-tools/latest/bin/sdkmanager" --licenses > /dev/null || true

      - name: Setup Gradle
        uses: gradle/actions/setup-gradle@v4

      - name: Build debug APK (unprivileged / EasyEUICC)
        run: ./gradlew --no-daemon :app-unpriv:assembleDebug

      - name: Upload APK
        uses: actions/upload-artifact@v4
        with:
          name: easyeuicc-debug-apk
          path: app-unpriv/build/outputs/apk/debug/*.apk
          if-no-files-found: error
          
