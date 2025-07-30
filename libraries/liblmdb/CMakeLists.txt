# 设置 CMake 最低版本和项目名称
cmake_minimum_required(VERSION 3.10)
project(liblmdb)

# 设置编译器选项
set(CMAKE_C_STANDARD 99)
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -Wextra -Wno-unused-parameter -Wbad-function-cast -Wuninitialized -O2 -g -pthread")

# 设置输出目录
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/bin)

# 源文件列表
set(LIB_SOURCES
    mdb.c
    midl.c
)

set(TEST_SOURCES
    mtest.c
    mtest2.c
    mtest3.c
    mtest4.c
    mtest5.c
    mtest6.c
    mplay.c
)

set(TOOL_SOURCES
    mdb_stat.c
    mdb_copy.c
    mdb_dump.c
    mdb_load.c
    mdb_drop.c
)

# 添加静态库和共享库
add_library(lmdb_static STATIC ${LIB_SOURCES})
add_library(lmdb SHARED ${LIB_SOURCES})  # 注意：目标名直接为 lmdb

# 设置共享库的编译标志（-fPIC）
set_target_properties(lmdb PROPERTIES
        POSITION_INDEPENDENT_CODE TRUE
)

# 添加工具和测试程序
foreach(src IN LISTS TEST_SOURCES)
    add_executable(${src} ${src})
    target_link_libraries(${src} lmdb_static pthread)
endforeach()

foreach(src IN LISTS TOOL_SOURCES)
    add_executable(${src} ${src})
    target_link_libraries(${src} lmdb_static pthread)
endforeach()

# 安装规则
install(TARGETS lmdb_static lmdb
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib)

install(FILES lmdb.h
        DESTINATION include)

install(PROGRAMS ${TEST_SOURCES} ${TOOL_SOURCES}
        DESTINATION bin)

install(FILES
    mdb_stat.1
    mdb_copy.1
    mdb_dump.1
    mdb_load.1
    mdb_drop.1
    DESTINATION share/man/man1)
