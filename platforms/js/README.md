Building OpenCV.js by Emscripten
====================

[Download and install Emscripten](https://emscripten.org/docs/getting_started/downloads.html).

Execute `build_js.py` script:
```
emcmake python <opencv_src_dir>/platforms/js/build_js.py <build_dir>
```

If everything is fine, a few minutes later you will get `<build_dir>/bin/opencv.js`. You can add this into your web pages.

Find out more build options by `-h` switch.

For detailed build tutorial, check out `<opencv_src_dir>/doc/js_tutorials/js_setup/js_setup/js_setup.markdown`.

## Custom build for ARGOS Identity

1. 기본적인 절차는 아래 문서를 따릅니다.
   - https://docs.opencv.org/4.10.0/d4/da1/tutorial_js_setup.html
   - https://emscripten.org/docs/getting_started/downloads.html
2. 현재 사용중인 버전
   - OpenCV version 4.10.0
   - Emscripten & Emscripten SDK latest version (3.1.64)
     - OpenCV 에서 권장하는 버전인 Emscripten 2.0.10 버전을 Apple Silicon Mac 에서 사용할때는 Rosetta 를 사용해서 Terminal 을 열어야합니다.
       - Mac Terminal 정보(CMD+I)에서 `Rosetta를 사용하여 열기` 체크
       - 설치 후 체크 해제
3. 기본 설정으로 build 해서 module 형태로 사용하면 `Module is not defined` 에러가 발생하고, Promise 를 반환하기 때문에 반드시 아래 내용을 따라야합니다.
   - ~~js 파일 하단의 `Module` 앞에 `var` 추가~~ -> modules/js/src/make_umd.py 파일에 추가함.
     - https://github.com/TechStark/opencv-js/issues/71#issuecomment-2239235741
     - https://github.com/TechStark/opencv-js/blob/main/dist/opencv.js.patch
   - ~~build 옵션에 `WASM_ASYNC_COMPILATION=0`~~ 추가 -> modules/js/CMakeLists.txt 파일에 추가함.
     - https://emscripten.org/docs/tools_reference/settings_reference.html?highlight=es6#wasm-async-compilation
     - https://github.com/TechStark/opencv-js/blob/main/.github/workflows/build-opencv.yml#L40
     - ~~`--build_wasm --build_flags="-s WASM_ASYNC_COMPILATION=0 "` (마지막에 공백이 있어야 cmake 옵션이 정상적으로 만들어졌음)~~
4. Build & copy
   - `emcmake python3 ./platforms/js/build_js.py build_js_{version} --build_wasm`
   - `cp build_js_{version}/bin/opencv.js {path_to_opencv-js-custom}/dist`
