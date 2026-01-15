cmake_minimum_required(VERSION 3.10)
project(latex_document NONE)

# Find required LaTeX tools
find_program(PDFLATEX pdflatex REQUIRED)
find_program(BIBTEX bibtex)

# Main document name (without .tex)
set(MAIN_TEX main)

# Output directory
set(OUTPUT_DIR ${CMAKE_BINARY_DIR}/output)
file(MAKE_DIRECTORY ${OUTPUT_DIR})

# Custom build command
add_custom_command(
    OUTPUT ${OUTPUT_DIR}/${MAIN_TEX}.pdf
    COMMAND ${PDFLATEX}
            -interaction=nonstopmode
            -output-directory=${OUTPUT_DIR}
            ${CMAKE_SOURCE_DIR}/latex_files/${MAIN_TEX}.tex
    COMMAND ${BIBTEX}
            ${OUTPUT_DIR}/${MAIN_TEX}
            || true
    COMMAND ${PDFLATEX}
            -interaction=nonstopmode
            -output-directory=${OUTPUT_DIR}
            ${CMAKE_SOURCE_DIR}/latex_files/${MAIN_TEX}.tex
    COMMAND ${PDFLATEX}
            -interaction=nonstopmode
            -output-directory=${OUTPUT_DIR}
            ${CMAKE_SOURCE_DIR}/latex_files/${MAIN_TEX}.tex
    DEPENDS
            ${CMAKE_SOURCE_DIR}/latex_files/*.tex
            ${CMAKE_SOURCE_DIR}/latex_files/${MAIN_TEX}.tex
    WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}/latex_files/
    COMMENT "Building LaTeX document"
)

# Custom target
add_custom_target(
    pdf ALL
    DEPENDS ${OUTPUT_DIR}/${MAIN_TEX}.pdf
)