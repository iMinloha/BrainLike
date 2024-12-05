# Test/CMakeLists.txt
enable_testing()

# 添加可执行文件进行测试
add_executable(${PROJECT_NAME}_TEST TestMain.cpp)

# 链接库
target_link_libraries(${PROJECT_NAME}_TEST ${PROJECT_NAME} ${OpenCV_LIBS})

# 注册测试
add_test(NAME ${PROJECT_NAME}_TEST COMMAND ${PROJECT_NAME}_TEST)
