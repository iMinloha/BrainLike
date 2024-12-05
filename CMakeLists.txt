cmake_minimum_required(VERSION 3.26)
project(BrainLike)

set(CMAKE_CXX_STANDARD 17)

# 设置 OpenCV 路径
set(OpenCV_DIR E:/opencv/build/install/x64/mingw/lib)

# 查找 OpenCV
find_package(OpenCV REQUIRED)

# 添加测试文件目录
add_subdirectory(Test)

# 自动收集所有头文件
file(GLOB HEADERS "include/*.h")  # 收集根目录 include 下的所有头文件

# 将项目根目录下的头文件目录添加到编译路径
include_directories(${CMAKE_SOURCE_DIR}/include)

file(GLOB_RECURSE SOURCE_FILES "src/*.*")

# 创建静态库（.lib）
add_library(${PROJECT_NAME} STATIC ${SOURCE_FILES})

# 链接 OpenCV 库
target_link_libraries(${PROJECT_NAME} ${OpenCV_LIBS})

# 设置导出符号（对于 Windows，使用 __declspec(dllexport)）
target_compile_definitions(${PROJECT_NAME} PRIVATE ${PROJECT_NAME}_EXPORTS)

# 设置导入头文件
target_include_directories(${PROJECT_NAME} PRIVATE ${CMAKE_SOURCE_DIR}/include)

# 收集所有头文件
file(GLOB HEADERS "${CMAKE_SOURCE_DIR}/include/*.h")

# 创建一个合并后的头文件, 保存在构建目录下
set(MERGED_HEADERS_PATH "${CMAKE_BINARY_DIR}/lib/lib${PROJECT_NAME}.h")

# 自定义命令将所有头文件合并
add_custom_command(
        TARGET ${PROJECT_NAME} POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E echo "Merging all headers into ${MERGED_HEADERS_PATH}..."
        COMMAND ${CMAKE_COMMAND} -E copy_if_different ${HEADERS} ${MERGED_HEADERS_PATH}
        COMMAND ${CMAKE_COMMAND} -E echo "Finished merging headers."
)

# 设置静态库输出目录（.lib）
set_target_properties(${PROJECT_NAME} PROPERTIES
        ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib
        LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib
)

# 安装静态库和合并的头文件
install(TARGETS ${PROJECT_NAME} DESTINATION lib)  # 安装静态库到 lib
install(FILES "${CMAKE_BINARY_DIR}/lib${PROJECT_NAME}.h" DESTINATION include)   # 安装合并的头文件到 include
