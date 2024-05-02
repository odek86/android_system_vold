cmake_minimum_required(VERSION 3.0)

project(vold)

set(CMAKE_CXX_STANDARD 17)

# Include directories
include_directories(
    frameworks/native/aidl/binder
    frameworks/base/core/java
)

# Library: libvold_binder
add_library(libvold_binder STATIC)
target_sources(libvold_binder PRIVATE vold_aidl)
target_link_libraries(libvold_binder PRIVATE libbinder libutils)

# Headers: libvold_headers
add_library(libvold_headers INTERFACE)
target_include_directories(libvold_headers INTERFACE ${CMAKE_CURRENT_SOURCE_DIR})

# Static library: libvold
add_library(libvold STATIC
    AppFuseUtil.cpp
    Benchmark.cpp
    Checkpoint.cpp
    CryptoType.cpp
    EncryptInplace.cpp
    FileDeviceUtils.cpp
    FsCrypt.cpp
    IdleMaint.cpp
    KeyBuffer.cpp
    KeyStorage.cpp
    KeyUtil.cpp
    Keystore.cpp
    Loop.cpp
    MetadataCrypt.cpp
    MoveStorage.cpp
    NetlinkHandler.cpp
    NetlinkManager.cpp
    Process.cpp
    Utils.cpp
    VoldNativeService.cpp
    VoldNativeServiceValidation.cpp
    VoldUtil.cpp
    VolumeManager.cpp
    cryptfs.cpp
    fs/Exfat.cpp
    fs/Ext4.cpp
    fs/F2fs.cpp
    fs/Ntfs.cpp
    fs/Vfat.cpp
    model/Disk.cpp
    model/EmulatedVolume.cpp
    model/ObbVolume.cpp
    model/PrivateVolume.cpp
    model/PublicVolume.cpp
    model/StubVolume.cpp
    model/VolumeBase.cpp
    model/VolumeEncryption.cpp
)
target_link_libraries(libvold PRIVATE libvold_headers ${CMAKE_DL_LIBS})

# Binary: vold
add_executable(vold main.cpp)
target_link_libraries(vold PRIVATE libvold)

# Binary: vdc
add_executable(vdc vdc.cpp Utils.cpp)
target_link_libraries(vdc PRIVATE libbase libbinder libcutils liblogwrap libselinux libutils libvold_binder)

# Binary: secdiscard
add_executable(secdiscard FileDeviceUtils.cpp secdiscard.cpp)
target_link_libraries(secdiscard PRIVATE libbase)

# Binary: vold_prepare_subdirs
add_executable(vold_prepare_subdirs vold_prepare_subdirs.cpp Utils.cpp)
target_link_libraries(vold_prepare_subdirs PRIVATE libbase libcutils liblogwrap libselinux libutils libvold_binder)
