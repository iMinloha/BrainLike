cmake_minimum_required(VERSION 3.26)
project(BrainLike)

set(CMAKE_CXX_STANDARD 17)

# 设置 OpenCV 路径
set(OpenCV_DIR E:/opencv/build/install/x64/mingw/lib)

# 查找 OpenCV
find_package(OpenCV REQUIRED)

# 添加源文件目录
add_subdirectory(src)

# 添加测试文件目录
add_subdirectory(Test)

# 自动收集所有头文件
file(GLOB HEADERS "include/*.h")  # 收集根目录 include 下的所有头文件

# 将项目根目录下的头文件目录添加到编译路径
include_directories(${CMAKE_SOURCE_DIR} include)

# 设置代码和头
SET(S src/library.cpp)
SET(H include/test.h)

# 添加library文件
add_library(${PROJECT_NAME} SHARED ${SRC_FILES} ${S} ${H})

# 链接 OpenCV库
target_link_libraries(${PROJECT_NAME} ${OpenCV_LIBS})
# 设置导出符号（对于 Windows，使用 __declspec(dllexport)）
target_compile_definitions(${PROJECT_NAME} PRIVATE ${PROJECT_NAME}_EXPORTS)
# 设置导入头文件们
target_include_directories(${PROJECT_NAME} PRIVATE ${CMAKE_SOURCE_DIR})


# 收集所有头文件
file(GLOB HEADERS "${CMAKE_SOURCE_DIR}/include/*.h")

# 创建一个合并后的头文件
set(MERGED_HEADERS_PATH "${CMAKE_BINARY_DIR}/lib${PROJECT_NAME}.h")


# 自定义命令将所有头文件合并
add_custom_command(
        TARGET ${PROJECT_NAME} POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E echo "Merging all headers into ${MERGED_HEADERS_PATH}..."
        COMMAND ${CMAKE_COMMAND} -E copy_if_different ${HEADERS} ${MERGED_HEADERS_PATH}
        COMMAND ${CMAKE_COMMAND} -E echo "Finished merging headers."
)

# 安装动态库和头文件
install(TARGETS ${PROJECT_NAME} DESTINATION lib)  # 安装动态库到 lib
install(FILES ${CMAKE_SOURCE_DIR}/library.h DESTINATION include)   # 安装头文件到 include
