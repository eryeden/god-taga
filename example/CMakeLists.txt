add_executable(test1 test1.cpp)
list(APPEND EXECUTABLE_TARGETS test1)


foreach (EXECUTABLE_TARGET IN LISTS EXECUTABLE_TARGETS)

    target_link_libraries(${EXECUTABLE_TARGET}
            spdlog::spdlog
            fmt::fmt
            box2d
            )

endforeach (EXECUTABLE_TARGET IN LISTS EXECUTABLE_TARGETS)
