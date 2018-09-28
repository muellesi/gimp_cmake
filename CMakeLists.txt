CMAKE_MINIMUM_REQUIRED(VERSION 2.8)
Project(GIMP-complete)

include(${CMAKE_ROOT}/Modules/ExternalProject.cmake)

SET(DOWNLOADDIR download)
SET(PREFIX ${CMAKE_SOURCE_DIR})
SET(INSTALLPREFIX ${PREFIX}/build)

ExternalProject_Add(babl
 PREFIX ${PREFIX}
 INSTALL_DIR ${INSTALLPREFIX}
 GIT_REPOSITORY git@gitlab.gnome.org:GNOME/babl.git
 GIT_SHALLOW TRUE
 GIT_PROGRESS TRUE
 CONFIGURE_COMMAND ${PREFIX}/src/babl/autogen.sh --prefix=${INSTALLPREFIX} --disable-docs
 BUILD_COMMAND make
 INSTALL_COMMAND make install
)

ExternalProject_Add(gegl
 DEPENDS babl
 PREFIX ${PREFIX}
 INSTALL_DIR ${INSTALLPREFIX}
 GIT_REPOSITORY git@gitlab.gnome.org:GNOME/gegl.git
 GIT_SHALLOW TRUE
 GIT_PROGRESS TRUE
 CONFIGURE_COMMAND ${PREFIX}/src/gegl/autogen.sh --prefix=${INSTALLPREFIX} --enable-gtk-doc-html=no --disable-docs
 BUILD_COMMAND make
 INSTALL_COMMAND make install
)

ExternalProject_Add(libmypaint
 DEPENDS babl gegl
 PREFIX ${PREFIX}
 INSTALL_DIR ${INSTALLPREFIX}
 GIT_REPOSITORY https://github.com/mypaint/libmypaint.git
 GIT_TAG origin/tags/v1.3.0
 GIT_SHALLOW TRUE
 GIT_PROGRESS TRUE
 CONFIGURE_COMMAND ${PREFIX}/src/libmypaint/autogen.sh && ${PREFIX}/src/libmypaint/configure --prefix=${INSTALLPREFIX}
 BUILD_COMMAND make
 INSTALL_COMMAND make install
)

ExternalProject_Add(mypaint-brushes
 PREFIX ${PREFIX}
 INSTALL_DIR ${INSTALLPREFIX}
 GIT_REPOSITORY https://github.com/Jehan/mypaint-brushes.git
 GIT_TAG origin/v1.3.x
 GIT_SHALLOW TRUE
 GIT_PROGRESS TRUE
 CONFIGURE_COMMAND ${PREFIX}/src/mypaint-brushes/autogen.sh && ${PREFIX}/src/mypaint-brushes/configure --prefix=${INSTALLPREFIX}
 BUILD_COMMAND make
 INSTALL_COMMAND make install
)

ExternalProject_Add(gimp
 DEPENDS babl gegl libmypaint mypaint-brushes
 PREFIX ${PREFIX}
 INSTALL_DIR ${INSTALLPREFIX}
 GIT_REPOSITORY git@gitlab.gnome.org:GNOME/gimp.git
 GIT_SHALLOW TRUE
 GIT_PROGRESS TRUE
 GIT_TAG origin/gimp-2-10
 CONFIGURE_COMMAND ${PREFIX}/src/gimp/autogen.sh --prefix=${INSTALLPREFIX} --disable-python --disable-gtk-doc --with-gimpdir=GIMP/git-master
 BUILD_COMMAND make
 INSTALL_COMMAND make install
)