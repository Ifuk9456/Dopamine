cmake_minimum_required(VERSION 3.15)
project(Dopamine C)

# 设置 C 标准
set(CMAKE_C_STANDARD 99)
set(CMAKE_C_STANDARD_REQUIRED ON)

# 设置编译选项（等同于 -Wall）
add_compile_options(-Wall)

# 包含头文件目录
include_directories(${PROJECT_SOURCE_DIR}/NorAdrenaline/ValveSDK)

# 收集源文件，只选 pm_shared 文件夹下的 C 文件
file(GLOB PM_SHARED_SOURCES
    "NorAdrenaline/ValveSDK/pm_shared/*.c"
)

# 添加可执行文件
add_executable(Dopamine ${PM_SHARED_SOURCES})

# 链接 Windows 库
target_link_libraries(Dopamine
    user32
    gdi32
    kernel32
    winmm
)

# 设置输出可执行文件名（Windows 下自动带 .exe）
set_target_properties(Dopamine PROPERTIES OUTPUT_NAME "Dopamine")
