project(TianLi.TruthEye.Inface VERSION 0.0.1)

# define 
add_definitions(-DTIANLI_TRUTHEYE_EXPORTS)
add_definitions(-DTIANLI_TRUTHEYE_IMPL)

# 添加本地第三方库
include_directories(${ThirdPartyDir}/cpr/include)
include_directories(${ThirdPartyDir}/spdlog/include)
#include_directories("../3rdparty/minizip/include")
# 添加库搜索路径
link_directories(${ThirdPartyDir}/cpr/lib)
link_directories(${ThirdPartyDir}/spdlog/lib)
#link_directories("../3rdparty/minizip/lib")

if(MSVC)
    set(VERSION_RC "${CMAKE_BINARY_DIR}/inface.version.rc")
    configure_file(${CMAKE_CURRENT_SOURCE_DIR}/inface.version.rc.in "${VERSION_RC}")
endif()

# 将源代码添加到此项目的可执行文件
add_library(${PROJECT_NAME} SHARED ${VERSION_RC}
	"TianLi.TruthEye.Inface.cpp" 
)

set_property(TARGET ${PROJECT_NAME} PROPERTY MSVC_RUNTIME_LIBRARY "MultiThreaded")

# link 3rdparty lib
target_link_libraries(${PROJECT_NAME} PRIVATE tianli.cpr tianli.libcurl Crypt32)
target_link_libraries(${PROJECT_NAME} PRIVATE tianli.spdlog $<$<BOOL:${MSVC}>:ws2_32>)

# used utf-8 encoding
target_compile_options(${PROJECT_NAME} PRIVATE "/utf-8")

# copy version.rc to output dir
add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
	COMMAND ${CMAKE_COMMAND} -E copy_if_different
		"${CMAKE_CURRENT_SOURCE_DIR}/version"
		$<TARGET_FILE_DIR:${PROJECT_NAME}>)
