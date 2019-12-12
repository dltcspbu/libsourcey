cmake_minimum_required(VERSION 3.8)
project(LibSourcey)

set(CMAKE_CXX_STANDARD 17)

set(CMAKE_BUILD_TYPE "Release")

add_definitions(-DWEBRTC_POSIX)

find_path(OPENSSL_INCLUDE_DIR
        NAMES openssl/ssl.h
        PATHS
        boringssl/src/include
        NO_DEFAULT_PATH)

find_path(WEBRTC_INCLUDE_DIR
        NAMES
        call/rtp_config.h
        PATHS
        webrtc-28114-9863f3d-linux-x64/include
        NO_DEFAULT_PATH)

find_path(ABSL_INCLUDE_DIR
        NAMES
        absl/types/optional.h
        PATHS
        webrtc-28114-9863f3d-linux-x64/include/third_party/abseil-cpp
        NO_DEFAULT_PATH)


include_directories(
        ${OPENSSL_INCLUDE_DIR}
        ${ABSL_INCLUDE_DIR}
        ${WEBRTC_INCLUDE_DIR}
)

set(sourcefiles *.cpp)
set(headerfiles *.h)

file(GLOB_RECURSE sources
        base/${sourcefiles}
        json/${sourcefiles}
        crypto/${sourcefiles}
        net/${sourcefiles}
        http/${sourcefiles}
        util/${sourcefiles}
        socketio/${sourcefiles}
        stun/${sourcefiles}
        symple/${sourcefiles}
        turn/${sourcefiles}
        webrtc/${sourcefiles}
        src/${sourcefiles}
        http-parser/*.c
        )

file(GLOB_RECURSE headers
        base/${headerfiles}
        json/${headerfiles}
        crypto/${headerfiles}
        net/${headerfiles}
        http/${headerfiles}
        util/${headerfiles}
        socketio/${headerfiles}
        stun/${headerfiles}
        symple/${headerfiles}
        turn/${headerfiles}
        webrtc/${headerfiles}
        src/${headerfiles}
        rapidjson/${headerfiles}
        uv/${headerfiles}
        http-parser/${headerfiles}
        )

file(GLOB_RECURSE statics
        *.a
        )

set(sourcedir src)
include_directories(${sourcedir})

if (CMAKE_BUILD_TYPE STREQUAL "Debug")
    message("debug build")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fPIC -g3 -ggdb3 -Wall -Wpedantic")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC -g3 -ggdb3 -Wall -Wpedantic")
else ()
    message("release build")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fPIC -O3 -march=native -ffast-math -flto")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC -O3 -march=native -ffast-math -flto")
endif ()

add_library(${PROJECT_NAME}
        SHARED
	uv.h
        json.hpp
        ${sources}
        ${headers})

target_link_libraries(${PROJECT_NAME}
        ${statics}
        pthread
        )
