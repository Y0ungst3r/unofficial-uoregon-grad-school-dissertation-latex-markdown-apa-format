cmake_minimum_required(VERSION 3.10)
project(latex_document NONE)

# Find required LaTeX tools
find_program(PDFLATEX pdflatex REQUIRED)
find_program(BIBTEX bibtex)
find_program(BIBER biber REQUIRED)

# Main document name (without .tex)
set(MAIN_TEX main)

# Output directory
set(OUTPUT_DIR ${CMAKE_BINARY_DIR}/output)
file(MAKE_DIRECTORY ${OUTPUT_DIR})

# Copy sty files into output directory
file(GLOB STY_FILES ${CMAKE_SOURCE_DIR}/sty_files/*.sty)

foreach(sty ${STY_FILES})
        configure_file(${sty} ${OUTPUT_DIR}/ COPYONLY)
endforeach()

# config
set(STY_DIR ${CMAKE_SOURCE_DIR}/sty_files)
# Custom build command
add_custom_command(
    OUTPUT ${OUTPUT_DIR}/${MAIN_TEX}.pdf
    COMMAND ${PDFLATEX}
            -interaction=nonstopmode
            -include-directory=${STY_DIR}
            -output-directory=${OUTPUT_DIR}
            ${CMAKE_SOURCE_DIR}/latex_files/${MAIN_TEX}.tex
    COMMAND ${BIBER}
            ${OUTPUT_DIR}/${MAIN_TEX}
            || true
    COMMAND ${PDFLATEX}
            -include-directory=${STY_DIR}
            -interaction=nonstopmode
            -output-directory=${OUTPUT_DIR}
            ${CMAKE_SOURCE_DIR}/latex_files/${MAIN_TEX}.tex
    COMMAND ${PDFLATEX}
            -include-directory=${STY_DIR}
            -interaction=nonstopmode
            -output-directory=${OUTPUT_DIR}
            ${CMAKE_SOURCE_DIR}/latex_files/${MAIN_TEX}.tex
    DEPENDS
            ${STY_DIR}
            ${CMAKE_SOURCE_DIR}/latex_files/DisappearingTrackPaper/*.tex
            ${CMAKE_SOURCE_DIR}/latex_files/*.tex
            ${CMAKE_SOURCE_DIR}/latex_files/${MAIN_TEX}.tex
    WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}/latex_files/
    COMMENT "Building LaTeX document"
)

# Custom target
add_custom_target(
    pdf ALL
    DEPENDS ${OUTPUT_DIR}/${MAIN_TEX}.pdf
    DEPENDS ${STY_FILES}
)