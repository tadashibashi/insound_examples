add_subdirectory(common)

# Symlinks for the Android project
set (TARGET_ASSETS_FOLDER ${CMAKE_CURRENT_SOURCE_DIR}/android/app/src/main/assets/assets)
if (NOT EXISTS ${TARGET_ASSETS_FOLDER})
    execute_process(COMMAND ${CMAKE_COMMAND} -E create_symlink
        ${CMAKE_CURRENT_SOURCE_DIR}/assets ${TARGET_ASSETS_FOLDER})
endif()

# Copy this project's root directory as a symlink for Android project's ability to access it as an added subdirectory
# TODO: This causes problems in some IDE's like CLion that crash, probably due to recursive indexing.
# Users would need to download a plugin like "Exclude Symlinks" to prevent this.
set (TARGET_ROOT_SYMLINK ${CMAKE_CURRENT_SOURCE_DIR}/android/app/jni/insound_examples)
if (NOT EXISTS ${TARGET_ROOT_SYMLINK})
    execute_process(COMMAND ${CMAKE_COMMAND} -E create_symlink
        ${INSOUND_EXAMPLES_ROOT_DIR} ${TARGET_ROOT_SYMLINK})
endif()
