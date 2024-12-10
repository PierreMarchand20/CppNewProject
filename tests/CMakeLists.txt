add_executable(first_test first_test.cpp)
target_link_libraries(first_test PUBLIC cpp_new_project)
add_test(first_test first_test)
