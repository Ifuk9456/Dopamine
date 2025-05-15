cmake_minimum_required(VERSION 3.15)
project(Dopamine C)

# 递归查找所有 C 文件
file(GLOB_RECURSE SRC_FILES
    "${PROJECT_SOURCE_DIR}/NorAdrenaline/**/*.c"
)

# 添加头文件目录
include_directories(
    ${PROJECT_SOURCE_DIR}/NorAdrenaline/ValveSDK/common
    ${PROJECT_SOURCE_DIR}/NorAdrenaline/ValveSDK/pm_shared
)

# 自动检测 C 文件中的 #include
set(MISSING_HEADERS "")
foreach(source_file ${SRC_FILES})
    file(READ "${source_file}" file_content)
    string(REGEX MATCHALL "#include[ ]*\"([^\"]+)\"" includes "${file_content}")
    foreach(include ${includes})
        string(REGEX REPLACE "#include[ ]*\"" "" header_path "${include}")
        string(REGEX REPLACE "\"" "" header_path "${header_path}")
        
        # 检查头文件是否存在于指定的 include 目录
        set(found_header FALSE)
        foreach(include_dir ${CMAKE_INCLUDE_PATH} ${CMAKE_SYSTEM_INCLUDE_PATH} ${PROJECT_SOURCE_DIR}/NorAdrenaline/ValveSDK/common ${PROJECT_SOURCE_DIR}/NorAdrenaline/ValveSDK/pm_shared)
            if(EXISTS "${include_dir}/${header_path}")
                set(found_header TRUE)
                break()
            endif()
        endforeach()

        if(NOT found_header)
            message(WARNING "未找到头文件: ${header_path} (引用自 ${source_file})")
            set(MISSING_HEADERS TRUE)
        endif()
    endforeach()
endforeach()

if(MISSING_HEADERS)
    message(FATAL_ERROR "检测到头文件缺失，请检查上面警告信息。")
endif()

# 添加可执行文件
add_executable(Dopamine ${SRC_FILES})

# 链接依赖
target_link_libraries(Dopamine
    user32
    gdi32
    kernel32
    winmm
)

# 设置输出可执行文件名（Windows 下自动带 .exe）
set_target_properties(Dopamine PROPERTIES OUTPUT_NAME "Dopamine")
