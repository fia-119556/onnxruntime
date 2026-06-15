# 16KB対応 AAR作成

## Docker起動
```bash
$docker compose up -d
$docker exec -it ort_android_build bash
```

## 作成
```bash
$python3 tools/ci_build/build.py \
  --build_dir /build \
  --android \
  --build_java \
  --android_ndk_path $ANDROID_NDK_ROOT \
  --android_sdk_path $ANDROID_SDK_ROOT \
  --android_abi arm64-v8a \
  --android_api 21 \
  --cmake_extra_defines CMAKE_SHARED_LINKER_FLAGS="-Wl,-z,max-page-size=16384 -Wl,-z,common-page-size=16384" \
  --config Release \
  --cmake_generator Ninja \
  --parallel \
  --skip_tests
```

## 出力場所
/build/Release/java/build/android/outputs/aar

### 16KB確認
aarを展開、コマンドを実行し、`LOAD`の`Align`が`0x4000 (16384)` であることを確認
```bash
$readelf -l libxxxx.so
```

### 取り出し
権限の関係でbuildフォルダはコンテナ内にあるのでホスト側にコピーする
```bash
$docker cp ort_android_build:/build/Release/java/build/android/outputs/aar ./../build/aar
```
