# reference

	 https://github.com/ffmpegwasm
	 https://github.com/goldvideo/decoder_wasm
	 https://github.com/sonysuqin/WasmVideoPlayer
	 https://github.com/ikuokuo/rtsp-wasm-player
	 https://github.com/xiangxud/webrtc_H265player

	 https://blog.csdn.net/superxxd/article/details/126439310
	 https://github.com/emscripten-core/emscripten/issues/9705
	 https://cloud.tencent.com/developer/ask/sof/814228/answer/1177155



# wasm compiler -> emscripten  

   emscripten : Emscripten is a complete compiler toolchain to WebAssembly, using LLVM, with a special focus on speed, size, and the Web platform.
   https://emscripten.org/docs/getting_started/downloads.html
 
### Get the emsdk repo
     git clone https://github.com/emscripten-core/emsdk.git

### Enter that directory
     cd emsdk

### Fetch the latest version of the emsdk (not needed the first time you clone)
     git pull

### Download and install the latest SDK tools.
     ./emsdk install latest	or   ./emsdk install 1.38.45

### Make the "latest" SDK "active" for the current user. (writes .emscripten file)
     ./emsdk activate latest    or   ./emsdk activate 1.38.45

### Activate PATH and other environment variables in the current terminal
     source ./emsdk_env.sh

# emcc ffmpeg -> libavcodec.a  libavutil.a  libswscale.a

   https://ffmpeg.org/
  
### Get the ffmpeg repo
     git clone git://source.ffmpeg.org/ffmpeg.git

### Enter that directory
     cd ffmpeg

### Config
     emconfigure ./configure --cc=emcc --cxx=em++ --ar=emar  --ranlib=emranlib --prefix=$(path) --cpu=generic --disable-avdevice --disable-avformat --disable-swresample --disable-postproc --disable-avfilter --disable-programs --disable-logging --disable-everything --disable-ffplay --disable-ffprobe --disable-asm --disable-doc --disable-devices --disable-network --disable-hwaccels --disable-parsers --disable-bsfs --disable-debug --disable-protocols --disable-indevs --disable-outdevs --enable-decoder=hevc --enable-parser=hevc --enable-decoder=h264  --enable-parser=h264

### make
     make
   
### install
     make install

# c/c++ -> decode.c

   Use ffmpeg to implement your functions and provide interfaces

### emcc ffmpeg -> libffmpeg.wasm libffmpeg.js

### shell
   export TOTAL_MEMORY=67108864
   export EXPORTED_FUNCTIONS="['_openDecoder' ,'_flushDecoder', '_closeDecoder','_decodeData','_main','_malloc','_free']"

   emcc decode_video.c ./dist/lib/libavcodec.a ./dist/lib/libavutil.a ./dist/lib/libswscale.a \
    -O2 \
    -I "./dist/include" \
    -s WASM=1 \
    -s TOTAL_MEMORY=${TOTAL_MEMORY} \
    -s EXPORTED_FUNCTIONS="${EXPORTED_FUNCTIONS}" \
    -s EXPORTED_RUNTIME_METHODS="['addFunction']" \
    -s RESERVED_FUNCTION_POINTERS=14 \
    -s FORCE_FILESYSTEM=1 \
    -o dist/libffmpeg.js
