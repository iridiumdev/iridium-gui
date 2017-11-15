cmake_minimum_required(VERSION 2.8)

include(CryptoNoteWallet.cmake)
project(${CN_PROJECT_NAME})

include( MunkeiVersionFromGit.cmake )
version_from_git(LOG ON)

execute_process(COMMAND git log -1 --pretty=format:%h
WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
OUTPUT_VARIABLE GIT_REVISION)

set(CRYPTONOTE_LIB iridium)

include_directories(${CMAKE_BINARY_DIR}
    src
    iridium/external
    iridium/include
    iridium/src)

find_package(Qt5Gui REQUIRED)
find_package(Qt5Widgets REQUIRED)

set(Boost_USE_STATIC_LIBS ON)
if(WIN32)
  set(Boost_USE_STATIC_RUNTIME OFF)
else(WIN32)
  set(Boost_USE_STATIC_RUNTIME ON)
endif(WIN32)

find_package(Boost 1.55 REQUIRED COMPONENTS date_time filesystem program_options regex serialization system thread chrono)

include_directories(${Boost_INCLUDE_DIRS})
link_directories(${Boost_LIBRARY_DIRS})

configure_file("iridium/src/version.h.in" "version.h")
configure_file("src/CryptoNoteWalletConfig.h.in" "CryptoNoteWalletConfig.h")

add_definitions(-DGIT_REVISION=\"${GIT_REVISION}\")
add_definitions(-DVERSION_VERSION=\"${VERSION}\")
set(LABEL_VERSION "${DEC_VER} build ${GIT_REVISION}")
add_definitions(-DLABEL_VERSION=\"${LABEL_VERSION}\")


message(STATUS "LABEL_VERSION ${LABEL_VERSION}")

set(CMAKE_AUTOMOC ON)

set(CRYPTONOTE_SOURCES
  iridium/external/miniupnpc/connecthostport.c
  iridium/external/miniupnpc/igd_desc_parse.c
  iridium/external/miniupnpc/minisoap.c
  iridium/external/miniupnpc/miniupnpc.c
  iridium/external/miniupnpc/miniwget.c
  iridium/external/miniupnpc/minixml.c
  iridium/external/miniupnpc/portlistingparse.c
  iridium/external/miniupnpc/receivedata.c
  iridium/external/miniupnpc/upnpcommands.c
  iridium/external/miniupnpc/upnpreplyparse.c
  iridium/src/Common/Base58.cpp
  iridium/src/Common/CommandLine.cpp
  iridium/src/Common/Util.cpp
  iridium/src/Common/StringTools.cpp
  iridium/src/Common/JsonValue.cpp
  iridium/src/Common/ConsoleTools.cpp
  iridium/src/Common/MemoryInputStream.cpp
  iridium/src/Common/PathTools.cpp
  iridium/src/Common/StdInputStream.cpp
  iridium/src/Common/StdOutputStream.cpp
  iridium/src/Common/StreamTools.cpp
  iridium/src/Common/StringOutputStream.cpp
  iridium/src/Common/StringView.cpp
  iridium/src/Common/VectorOutputStream.cpp
  iridium/src/crypto/blake256.c
  iridium/src/crypto/chacha8.c
  iridium/src/crypto/crypto-ops-data.c
  iridium/src/crypto/crypto-ops.c
  iridium/src/crypto/crypto.cpp
  iridium/src/crypto/groestl.c
  iridium/src/crypto/hash-extra-blake.c
  iridium/src/crypto/hash-extra-groestl.c
  iridium/src/crypto/hash-extra-jh.c
  iridium/src/crypto/hash-extra-skein.c
  iridium/src/crypto/hash.c
  iridium/src/crypto/jh.c
  iridium/src/crypto/keccak.c
  iridium/src/crypto/oaes_lib.c
  iridium/src/crypto/random.c
  iridium/src/crypto/skein.c
  iridium/src/crypto/slow-hash.c
  iridium/src/crypto/slow-hash.cpp
  iridium/src/crypto/tree-hash.c
  iridium/src/CryptoNoteCore/BlockchainIndices.cpp
  iridium/src/CryptoNoteCore/BlockchainMessages.cpp
  iridium/src/CryptoNoteCore/BlockIndex.cpp
  iridium/src/CryptoNoteCore/CoreConfig.cpp
  iridium/src/CryptoNoteCore/CryptoNoteBasic.cpp
  iridium/src/CryptoNoteCore/CryptoNoteTools.cpp
  iridium/src/CryptoNoteCore/Currency.cpp
  iridium/src/CryptoNoteCore/MinerConfig.cpp
  iridium/src/CryptoNoteCore/Transaction.cpp
  iridium/src/CryptoNoteCore/Account.cpp
  iridium/src/CryptoNoteCore/Blockchain.cpp
  iridium/src/CryptoNoteCore/Checkpoints.cpp
  iridium/src/CryptoNoteCore/CryptoNoteBasicImpl.cpp
  iridium/src/CryptoNoteCore/Core.cpp
  iridium/src/CryptoNoteCore/CryptoNoteFormatUtils.cpp
  iridium/src/CryptoNoteCore/CryptoNoteSerialization.cpp
  iridium/src/CryptoNoteCore/Difficulty.cpp
  iridium/src/CryptoNoteCore/IBlock.cpp
  iridium/src/CryptoNoteCore/Miner.cpp
  iridium/src/CryptoNoteCore/TransactionExtra.cpp
  iridium/src/CryptoNoteCore/TransactionPool.cpp
  iridium/src/CryptoNoteCore/TransactionPrefixImpl.cpp
  iridium/src/CryptoNoteCore/TransactionUtils.cpp
  iridium/src/InProcessNode/InProcessNode.cpp
  iridium/src/InProcessNode/InProcessNodeErrors.cpp
  iridium/src/NodeRpcProxy/NodeErrors.cpp
  iridium/src/NodeRpcProxy/NodeRpcProxy.cpp
  iridium/src/P2p/NetNodeConfig.cpp
  iridium/src/Serialization/BinaryInputStreamSerializer.cpp
  iridium/src/Serialization/BinaryOutputStreamSerializer.cpp
  iridium/src/Serialization/JsonInputValueSerializer.cpp
  iridium/src/Serialization/JsonOutputStreamSerializer.cpp
  iridium/src/Serialization/KVBinaryInputStreamSerializer.cpp
  iridium/src/Serialization/KVBinaryOutputStreamSerializer.cpp
  iridium/src/Serialization/SerializationOverloads.cpp
  iridium/src/Transfers/BlockchainSynchronizer.cpp
  iridium/src/Transfers/SynchronizationState.cpp
  iridium/src/Transfers/TransfersConsumer.cpp
  iridium/src/Transfers/TransfersContainer.cpp
  iridium/src/Transfers/TransfersSubscription.cpp
  iridium/src/Transfers/TransfersSynchronizer.cpp
  iridium/src/Wallet/LegacyKeysImporter.cpp
  iridium/src/Wallet/WalletAsyncContextCounter.cpp
  iridium/src/Wallet/WalletErrors.cpp
  iridium/src/WalletLegacy/KeysStorage.cpp
  iridium/src/WalletLegacy/WalletLegacy.cpp
  iridium/src/WalletLegacy/WalletHelper.cpp
  iridium/src/WalletLegacy/WalletLegacySerializer.cpp
  iridium/src/WalletLegacy/WalletLegacySerialization.cpp
  iridium/src/WalletLegacy/WalletTransactionSender.cpp
  iridium/src/WalletLegacy/WalletUnconfirmedTransactions.cpp
  iridium/src/WalletLegacy/WalletUserTransactionsCache.cpp
  iridium/src/System/ContextGroup.cpp
  iridium/src/System/Event.cpp
  iridium/src/System/EventLock.cpp
  iridium/src/System/InterruptedException.cpp
  iridium/src/System/Ipv4Address.cpp
  iridium/src/System/TcpStream.cpp
  iridium/src/HTTP/HttpRequest.cpp
  iridium/src/HTTP/HttpParser.cpp
  iridium/src/HTTP/HttpParserErrorCodes.cpp
  iridium/src/HTTP/HttpResponse.cpp
  iridium/src/Rpc/HttpClient.cpp
  iridium/src/Rpc/JsonRpc.cpp
  iridium/src/P2p/NetNode.cpp
  iridium/src/P2p/LevinProtocol.cpp
  iridium/src/P2p/NetNodeConfig.cpp
  iridium/src/P2p/PeerListManager.cpp
  iridium/src/CryptoNoteProtocol/CryptoNoteProtocolHandler.cpp
  iridium/src/Logging/ILogger.cpp
  iridium/src/Logging/LoggerGroup.cpp
  iridium/src/Logging/CommonLogger.cpp
  iridium/src/Logging/LoggerManager.cpp
  iridium/src/Logging/FileLogger.cpp
  iridium/src/Logging/StreamLogger.cpp
  iridium/src/Logging/ConsoleLogger.cpp
  iridium/src/Logging/LoggerMessage.cpp
  iridium/src/Logging/LoggerRef.cpp
  iridium/src/BlockchainExplorer/BlockchainExplorer.cpp
  iridium/src/BlockchainExplorer/BlockchainExplorerDataBuilder.cpp
  iridium/src/BlockchainExplorer/BlockchainExplorerErrors.cpp
)

file(GLOB_RECURSE SOURCES src/*.cpp)
file(GLOB_RECURSE HEADERS src/*.h)
file(GLOB_RECURSE FORMS src/gui/ui/*.ui)

set(QRC src/resources.qrc)

qt5_wrap_ui(UIS ${FORMS})
qt5_add_resources(RCC ${QRC})


if (WIN32)
  if (NOT MSVC)
	message(FATAL_ERROR "Only MSVC is supported on this platform")
  endif ()
  add_definitions(/D_CRT_SECURE_NO_WARNINGS /D_WIN32_WINNT=0x0600 /DSTATICLIB)
  include_directories(iridium/src/platform/msc)

  set(PLATFORM_DIR Windows)
  set(BUILD_PLATFORM WIN32)
  set(BUILD_RESOURCES src/cryptonotewallet.rc)
  set(QTMAIN Qt5::WinMain)

elseif (UNIX)
  set(CRYPTONOTE_SOURCES ${CRYPTONOTE_SOURCES} iridium/external/miniupnpc/minissdpc.c)
  if (APPLE)
    enable_language(ASM)
    file(GLOB_RECURSE OBJC_SOURCES src/*.mm)
    set(SOURCES ${SOURCES} ${OBJC_SOURCES})
    set(PLATFORM_DIR OSX)
    set(MACOSX_BUNDLE_INFO_STRING "${VERSION_MAJOR}.${VERSION_MINOR}.${VERSION_PATCH}")
    set(MACOSX_BUNDLE_LONG_VERSION_STRING "${VERSION_MAJOR}.${VERSION_MINOR}.${VERSION_PATCH}")
    set(MACOSX_BUNDLE_BUNDLE_NAME IridiumWallet)
    set(MACOSX_BUNDLE_SHORT_VERSION_STRING "${VERSION_MAJOR}.${VERSION_MINOR}.${VERSION_PATCH}")
    set(MACOSX_BUNDLE_BUNDLE_VERSION "${VERSION_MAJOR}.${VERSION_MINOR}.${VERSION_PATCH}")
    set(MACOSX_BUNDLE_COPYRIGHT "courtesy by steevebrush")

message( STATUS "Building MacOS version : ${VERSION}")

    find_package(Qt5PrintSupport REQUIRED)

    include_directories(/usr/include/malloc)

    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -maes -std=c++11 -stdlib=libc++")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=c11 -maes -D_DARWIN_C_SOURCE")
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -framework Cocoa -framework OpenGL -framework CoreFoundation -framework Carbon -framework IOKit -L/usr/lib")

    set(MACOSX_BUNDLE_ICON_FILE iridium.icns)
    set(APPLICATION_ICON src/images/iridium.icns)
    set_source_files_properties(${APPLICATION_ICON} PROPERTIES MACOSX_PACKAGE_LOCATION "Resources")

    set(BUILD_PLATFORM MACOSX_BUNDLE)
    set(BUILD_RESOURCES ${APPLICATION_ICON})
	
    GET_TARGET_PROPERTY(QT_LIB_DIR "${Qt5Widgets_LIBRARIES}" LOCATION)
    GET_FILENAME_COMPONENT(QT_LIB_DIR "${QT_LIB_DIR}" PATH)
  else(APPLE)
    set(PLATFORM_DIR Linux)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -maes -std=c++11")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -maes -std=c11")
  endif (APPLE)
endif ()

include_directories(iridium/src/Platform/${PLATFORM_DIR})
file(GLOB PLATFORM_SOURCES iridium/src/Platform/${PLATFORM_DIR}/System/*)
set(CRYPTONOTE_SOURCES ${CRYPTONOTE_SOURCES} ${PLATFORM_SOURCES})

add_library(${CRYPTONOTE_LIB} STATIC ${CRYPTONOTE_SOURCES})
set_target_properties(${CRYPTONOTE_LIB} PROPERTIES COMPILE_DEFINITIONS _GNU_SOURCE)
target_link_libraries(${CRYPTONOTE_LIB} ${Boost_LIBRARIES})

add_executable(${PROJECT_NAME} ${BUILD_PLATFORM} ${BUILD_RESOURCES} ${SOURCES} ${HEADERS} ${UIS} ${RCC})
set_target_properties(${PROJECT_NAME} PROPERTIES COMPILE_DEFINITIONS _GNU_SOURCE)
target_link_libraries(${PROJECT_NAME} ${Boost_LIBRARIES} ${QTMAIN} ${CRYPTONOTE_LIB})

if (APPLE)
  qt5_use_modules(${PROJECT_NAME} PrintSupport)
elseif (UNIX)
  target_link_libraries(${PROJECT_NAME} -lpthread)
elseif (WIN32)
  target_link_libraries(${PROJECT_NAME} Imm32 Iphlpapi Winmm)
endif (APPLE)

qt5_use_modules(${PROJECT_NAME} Widgets Gui)
