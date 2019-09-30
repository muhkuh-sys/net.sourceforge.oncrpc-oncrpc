cmake_minimum_required(VERSION 3.7)

PROJECT("net.sourceforge.oncrpc")

# Get the VCS version for the jonchki configuration.
INCLUDE(cmake/version.cmake)
INCLUDE(cmake/platform.cmake)
MESSAGE("Building for: ${JONCHKI_PLATFORM}")


# Enable tests.
ENABLE_TESTING()

# Set some installation folders.
INCLUDE(cmake/muhkuh_installation_folders.cmake)

#----------------------------------------------------------------------------
#
# Find all packages.
#

# Python is used for some test scripts.
FIND_PACKAGE(PythonInterp 2.7 REQUIRED)


#----------------------------------------------------------------------------
#
# Include the projects in the subdirectories.
#
ADD_SUBDIRECTORY(oncrpc)

#----------------------------------------------------------------------------
