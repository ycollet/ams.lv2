cmake_minimum_required(VERSION 3.10)

project(ams-lv2 CXX)

set(CMAKE_CXX_STANDARD 11)

find_package(PkgConfig)
pkg_check_modules(LVTK REQUIRED lvtk-2)
pkg_check_modules(LVTK_PLUGIN REQUIRED lvtk-plugin-2)
pkg_check_modules(LVTK_UI REQUIRED lvtk-ui-2)
pkg_check_modules(LVTK_GTKUI REQUIRED lvtk-gtkui-2)
pkg_check_modules(GTKMM24 REQUIRED gtkmm-2.4)
pkg_check_modules(GTK2 REQUIRED gtk+-2.0)
pkg_check_modules(CAIRO REQUIRED cairo)
pkg_check_modules(LV2 REQUIRED lv2)
pkg_check_modules(FFTW3 REQUIRED fftw3)

include_directories(
  src
  ${LVTK_INCLUDE_DIRS}
  ${LVTK_PLUGIN_INCLUDE_DIRS}
  ${LVTK_UI_INCLUDE_DIRS}
  ${LVTK_GTKUI_INCLUDE_DIRS}
  ${GTKMM24_INCLUDE_DIRS}
  ${GTK2_INCLUDE_DIRS}
  ${CAIRO_INCLUDE_DIRS}
  ${LV2_INCLUDE_DIRS}
  ${FFTW3_INCLUDE_DIRS}
)

set(PLUGIN_LIST
  absolute
  advenv
  amp
  amplitude
  analogmemory
  bitgrinder
  controltocv
  cvs
  cvtocontrol
  delay
  env
  haas
  hysteresis
  hztovc
  inv
  lfo2_tempo
  lfo2_freq
  lfo
  mixer_8ch
  mixer_4ch
  mixer_2ch
  mooglpf
  multiphase_lfo
  noise2
  percussiveenv
  ringmodulator
  samplehold
  slew
  stereomixer_2ch
  stereomixer_4ch
  stereomixer_8ch
  transport
  vcalin
  vcdelay
  vcenv
  vcpanning
  vcswitch
  vctohz
)

macro(build_plugin)
  add_library(${ARGV0} SHARED src/${ARGV0}.cpp src/synthdata.cpp)
  target_link_options(${ARGV0} PRIVATE "LINKER:-as-needed")
  set_target_properties(${ARGV0} PROPERTIES PREFIX "")
  target_compile_options(${ARGV0} PRIVATE "-DPLUGIN_CLASS=${ARGV0};-DURI_PREFIX=\"http://github.com/blablack/ams-lv2/\";-DPLUGIN_URI_SUFFIX=\"${ARGV0}\";-DPLUGIN_HEADER=\"src/${ARGV0}.hpp\"")
  target_link_libraries(${ARGV0} PRIVATE ${LVTK_LINK_LIBRARIES};${LVTK_PLUGIN_LINK_LIBRARIES})
endmacro()

foreach(PLUGIN ${PLUGIN_LIST})
  build_plugin(${PLUGIN})
endforeach()

macro(build_plugin_gui)
  add_library(${ARGV0} SHARED src/${ARGV0}.cpp src/synthdata.cpp src/dial.cpp src/labeleddial.cpp ${ARGV1})
  target_link_options(${ARGV0} PRIVATE "LINKER:-as-needed")
  set_target_properties(${ARGV0} PROPERTIES PREFIX "")
  target_compile_options(${ARGV0} PRIVATE "-DPLUGIN_CLASS=${ARGV0};-DURI_PREFIX=\"http://github.com/blablack/ams-lv2/\";-DPLUGIN_URI_SUFFIX=\"${ARGV0}\";-DPLUGIN_HEADER=\"src/${ARGV0}.hpp\"")
  target_link_libraries(${ARGV0} "${GTKMM24_LINK_LIBRARIES};${GTK2_LINK_LIBRARIES};${CAIRO_LINK_LIBRARIES};${LVTK_UI_LINK_LIBRARIES}")
endmacro()

set(PLUGIN_GUI_LIST
  amp_gui
  bitgrinder_gui
  delay_gui
  haas_gui
  hysteresis_gui
  hztovc_gui
  mixer_2ch_gui
  mixer_4ch_gui
  mixer_8ch_gui
  ringmodulator_gui
  samplehold_gui
  slew_gui
  vcdelay_gui
  vcswitch_gui
)

foreach(PLUGIN ${PLUGIN_GUI_LIST})
  build_plugin_gui(${PLUGIN})
endforeach()

set(PLUGIN_GUI_MY_BOX_LIST
  analogmemory_gui
  cvs_gui
  fftvocoder_gui
  lfo2_freq_gui
  lfo2_tempo_gui
  lfo_gui
  mooglpf_gui
  multiphase_lfo_gui
  noise2_gui
  stereomixer_2ch_gui
  stereomixer_4ch_gui
  stereomixer_8ch_gui
  vcaexp_gui
  vcalin_gui
  vcdoubledecay_gui
  vcenv_gui
  vcenvii_gui
  vcf_gui
  vco2_gui
  vco3_gui
  vcpanning_gui
  vctohz_gui
)

foreach(PLUGIN ${PLUGIN_GUI_MY_BOX_LIST})
  build_plugin_gui(${PLUGIN} src/my_box.cpp)
endforeach()

build_plugin_gui(env_gui "src/env_gui_scope.cpp;src/my_box.cpp")
build_plugin_gui(percussiveenv_gui "src/percussiveenv_gui_scope.cpp;src/my_box.cpp")
build_plugin_gui(advenv_gui "src/advenv_gui_scope.cpp;src/my_box.cpp")

macro(build_plugin_with_extra)
  add_library(${ARGV0} SHARED src/synthdata.cpp src/dial.cpp src/labeleddial.cpp ${ARGV1})
  target_link_options(${ARGV0} PRIVATE "LINKER:-as-needed")
  set_target_properties(${ARGV0} PROPERTIES PREFIX "")
  target_compile_options(${ARGV0} PRIVATE "${ARGV2};-DPLUGIN_CLASS=${ARGV0};-DURI_PREFIX=\"http://github.com/blablack/ams-lv2/\";-DPLUGIN_URI_SUFFIX=\"${ARGV0}\";-DPLUGIN_HEADER=\"src/${ARGV0}.hpp\"")
  target_link_libraries(${ARGV0} PRIVATE ${GTKMM24_LINK_LIBRARIES}
    ${GTK2_LINK_LIBRARIES}
    ${LVTK_PLUGIN_LINK_LIBRARIES}
  )
endmacro()

macro(build_plugin_gui_with_extra)
  add_library(${ARGV0} SHARED src/synthdata.cpp src/dial.cpp src/labeleddial.cpp ${ARGV1})
  target_link_options(${ARGV0} PRIVATE "LINKER:-as-needed")
  set_target_properties(${ARGV0} PROPERTIES PREFIX "")
  target_compile_options(${ARGV0} PRIVATE "${ARGV2};-DPLUGIN_CLASS=${ARGV0};-DURI_PREFIX=\"http://github.com/blablack/ams-lv2/\";-DPLUGIN_URI_SUFFIX=\"${ARGV0}\";-DPLUGIN_HEADER=\"src/${ARGV0}.hpp\"")
  target_link_libraries(${ARGV0} PRIVATE ${GTKMM24_LINK_LIBRARIES}
    ${GTK2_LINK_LIBRARIES}
    ${LVTK_UI_LINK_LIBRARIES}
  )
endmacro()

add_library(fftvocoder SHARED src/synthdata.cpp src/dial.cpp src/labeleddial.cpp)
target_link_options(fftvocoder PRIVATE "LINKER:-as-needed")
set_target_properties(fftvocoder PROPERTIES PREFIX "")
target_compile_options(fftvocoder PRIVATE "-DPLUGIN_CLASS=fftvocoder;-DURI_PREFIX=\"http://github.com/blablack/ams-lv2/\";-DPLUGIN_URI_SUFFIX=\"fftvocoder\";-DPLUGIN_HEADER=\"src/fftvocoder.hpp\"")
target_link_libraries(fftvocoder PRIVATE ${LVTK_LINK_LIBRARIES}
  ${GTKMM24_LINK_LIBRARIES}
  ${LVTK_PLUGIN_LINK_LIBRARIES}
  ${FFTW3_LINK_LIBRARIES}
)

build_plugin_with_extra(ad_2 "src/ad.cpp" "-DOUT_COUNT=2")
build_plugin_with_extra(ad_4 "src/ad.cpp" "-DOUT_COUNT=4")
build_plugin_with_extra(ad_6 "src/ad.cpp" "-DOUT_COUNT=6")
build_plugin_gui_with_extra(ad_2_gui "src/ad_gui.cpp;src/my_box.cpp" "-DOUT_COUNT=2")
build_plugin_gui_with_extra(ad_4_gui "src/ad_gui.cpp;src/my_box.cpp" "-DOUT_COUNT=4")
build_plugin_gui_with_extra(ad_6_gui "src/ad_gui.cpp;src/my_box.cpp" "-DOUT_COUNT=6")

build_plugin_with_extra(dynamicwaves_4 "src/dynamicwaves.cpp" "-DOSC_COUNT=4")
build_plugin_with_extra(dynamicwaves_6 "src/dynamicwaves.cpp" "-DOSC_COUNT=6")
build_plugin_with_extra(dynamicwaves_8 "src/dynamicwaves.cpp" "-DOSC_COUNT=8")
build_plugin_gui_with_extra(dynamicwaves_4_gui "src/dynamicwaves_gui.cpp;src/my_box.cpp;src/dynamicwaves_scope.cpp" "-DOSC_COUNT=4")
build_plugin_gui_with_extra(dynamicwaves_6_gui "src/dynamicwaves_gui.cpp;src/my_box.cpp;src/dynamicwaves_scope.cpp" "-DOSC_COUNT=6")
build_plugin_gui_with_extra(dynamicwaves_8_gui "src/dynamicwaves_gui.cpp;src/my_box.cpp;src/dynamicwaves_scope.cpp" "-DOSC_COUNT=8")

build_plugin_with_extra(vcorgan_4 "src/vcorgan.cpp" "-DOSC_COUNT=4")
build_plugin_with_extra(vcorgan_6 "src/vcorgan.cpp" "-DOSC_COUNT=6")
build_plugin_with_extra(vcorgan_8 "src/vcorgan.cpp" "-DOSC_COUNT=8")
build_plugin_gui_with_extra(vcorgan_4_gui "src/vcorgan_gui.cpp;src/my_box.cpp" "-DOSC_COUNT=4")
build_plugin_gui_with_extra(vcorgan_6_gui "src/vcorgan_gui.cpp;src/my_box.cpp" "-DOSC_COUNT=6")
build_plugin_gui_with_extra(vcorgan_8_gui "src/vcorgan_gui.cpp;src/my_box.cpp" "-DOSC_COUNT=8")

build_plugin_with_extra(dynamicwaves2_4 "src/dynamicwaves2.cpp" "-DOSC_COUNT=4")
build_plugin_with_extra(dynamicwaves2_6 "src/dynamicwaves2.cpp" "-DOSC_COUNT=6")
build_plugin_with_extra(dynamicwaves2_8 "src/dynamicwaves2.cpp" "-DOSC_COUNT=8")
build_plugin_gui_with_extra(dynamicwaves2_4_gui "src/dynamicwaves2_gui.cpp;src/my_box.cpp;src/dynamicwaves_scope.cpp" "-DOSC_COUNT=4")
build_plugin_gui_with_extra(dynamicwaves2_6_gui "src/dynamicwaves2_gui.cpp;src/my_box.cpp;src/dynamicwaves_scope.cpp" "-DOSC_COUNT=6")
build_plugin_gui_with_extra(dynamicwaves2_8_gui "src/dynamicwaves2_gui.cpp;src/my_box.cpp;src/dynamicwaves_scope.cpp" "-DOSC_COUNT=8")

build_plugin_with_extra(vcorgan2_4 "src/vcorgan2.cpp" "-DOSC_COUNT=4")
build_plugin_with_extra(vcorgan2_6 "src/vcorgan2.cpp" "-DOSC_COUNT=6")
build_plugin_with_extra(vcorgan2_8 "src/vcorgan2.cpp" "-DOSC_COUNT=8")
build_plugin_gui_with_extra(vcorgan2_4_gui "src/vcorgan2_gui.cpp;src/my_box.cpp" "-DOSC_COUNT=4")
build_plugin_gui_with_extra(vcorgan2_6_gui "src/vcorgan2_gui.cpp;src/my_box.cpp" "-DOSC_COUNT=6")
build_plugin_gui_with_extra(vcorgan2_8_gui "src/vcorgan2_gui.cpp;src/my_box.cpp" "-DOSC_COUNT=8")

build_plugin_with_extra(seq_8 "src/seq.cpp" "-DSTEP_COUNT=8")
build_plugin_with_extra(seq_12 "src/seq.cpp" "-DSTEP_COUNT=12")
build_plugin_with_extra(seq_16 "src/seq.cpp" "-DSTEP_COUNT=16")
build_plugin_with_extra(seq_24 "src/seq.cpp" "-DSTEP_COUNT=24")
build_plugin_with_extra(seq_32 "src/seq.cpp" "-DSTEP_COUNT=32")
build_plugin_gui_with_extra(seq_8_gui "src/seq_gui.cpp;src/my_box.cpp" "-DSTEP_COUNT=8")
build_plugin_gui_with_extra(seq_12_gui "src/seq_gui.cpp;src/my_box.cpp" "-DSTEP_COUNT=12")
build_plugin_gui_with_extra(seq_16_gui "src/seq_gui.cpp;src/my_box.cpp" "-DSTEP_COUNT=16")
build_plugin_gui_with_extra(seq_32_gui "src/seq_gui.cpp;src/my_box.cpp" "-DSTEP_COUNT=32")

include(GNUInstallDirs)

# Some macros to get all built targets
function(get_all_targets var)
  set(targets)
  get_all_targets_recursive(targets ${CMAKE_CURRENT_SOURCE_DIR})
  set(${var} ${targets} PARENT_SCOPE)
endfunction()

macro(get_all_targets_recursive targets dir)
  get_property(subdirectories DIRECTORY ${dir} PROPERTY SUBDIRECTORIES)
  foreach(subdir ${subdirectories})
    get_all_targets_recursive(${targets} ${subdir})
  endforeach()
  
  get_property(current_targets DIRECTORY ${dir} PROPERTY BUILDSYSTEM_TARGETS)
  foreach(t ${current_targets})
    get_target_property(target_type ${t} TYPE)
    if (NOT ${target_type} STREQUAL "INTERFACE_LIBRARY" AND NOT ${target_type} STREQUAL "UTILITY")
      list(APPEND ${targets} ${t})
    endif()
  endforeach()
endmacro()
  
get_all_targets(all_targets)
# message("All targets: ${all_targets}")

install(DIRECTORY ${CMAKE_SOURCE_DIR}/ams.lv2/ DESTINATION ${CMAKE_INSTALL_LIBDIR}/lv2/ams.lv2/)

foreach(TT ${all_targets})
  install(TARGETS ${TT} DESTINATION ${CMAKE_INSTALL_LIBDIR}/lv2/ams.lv2/)
endforeach()
