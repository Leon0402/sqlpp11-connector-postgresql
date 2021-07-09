# Copyright (c) 2015, Matthijs Möhlmann
# Copyright (c) 2016, Christian Dávid
# All rights reserved.
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions are met:
#
#   Redistributions of source code must retain the above copyright notice, this
#   list of conditions and the following disclaimer.
#
#   Redistributions in binary form must reproduce the above copyright notice,
#   this list of conditions and the following disclaimer in the documentation
#   and/or other materials provided with the distribution.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
# AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
# IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
# ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE
# LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR
# CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF
# SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS
# INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN
# CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE)
# ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
# POSSIBILITY OF SUCH DAMAGE.
add_library(sqlpp11-connector-postgresql)
add_library(sqlpp11::postgresql ALIAS sqlpp11-connector-postgresql)

target_sources(sqlpp11-connector-postgresql
    PRIVATE 
        bind_result.cpp
		connection.cpp
		exception.cpp
		prepared_statement.cpp
		detail/connection_handle.cpp
		detail/prepared_statement_handle.cpp
		result.cpp
)
target_compile_features(sqlpp11-connector-postgresql PRIVATE cxx_std_11)
target_link_libraries(sqlpp11-connector-postgresql PUBLIC sqlpp11::sqlpp11 PostgreSQL::PostgreSQL)
target_include_directories(sqlpp11-connector-postgresql PUBLIC
    $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
)


# Generate code coverage if enabled
if (CODE_COVERAGE AND CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang")
	# Add required flags for code coverage
	target_compile_options(sqlpp11-connector-postgresql PUBLIC -O0 --coverage)
	target_compile_options(sqlpp11-connector-postgresql-dynamic PUBLIC -O0 --coverage)
	target_link_options(sqlpp11-connector-postgresql INTERFACE --coverage)
	target_link_options(sqlpp11-connector-postgresql-dynamic INTERFACE --coverage)
endif()

# Keep this in sync with the releases in sqlpp11
set(sqlpp11-connector-postgresql_VERSION_MAJOR 0)
set(sqlpp11-connector-postgresql_VERSION_MINOR 54)
set(sqlpp11-connector-postgresql_VERSION_PATCH 0)
set(sqlpp11-connector-postgresql_VERSION_STRING ${sqlpp11-connector-postgresql_VERSION_MAJOR}.${sqlpp11-connector-postgresql_VERSION_MINOR}.${sqlpp11-connector-postgresql_VERSION_PATCH})
set_target_properties(sqlpp11-connector-postgresql PROPERTIES VERSION ${sqlpp11-connector-postgresql_VERSION_STRING}
	        SOVERSION ${sqlpp11-connector-postgresql_VERSION_MAJOR})

install(TARGETS sqlpp11-connector-postgresql
        EXPORT postgresqlTargets
        DESTINATION lib)

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/cmake/Sqlpp-connector-postgresqlConfigVersion.cmake"
  VERSION ${Upstream_VERSION}
  COMPATIBILITY AnyNewerVersion
)

export(EXPORT postgresqlTargets
  FILE "${CMAKE_CURRENT_BINARY_DIR}/cmake/Sqlpp-connector-postgresqlTargets.cmake"
  NAMESPACE Sqlpp::
)

install(EXPORT postgresqlTargets
  FILE Sqlpp-connector-postgresqlTargets.cmake
  NAMESPACE Sqlpp::
  DESTINATION ${ConfigPackageLocation}
)

install(
  FILES "${CMAKE_CURRENT_BINARY_DIR}/cmake/Sqlpp-connector-postgresqlConfigVersion.cmake"
  DESTINATION ${ConfigPackageLocation}
  COMPONENT Devel
)

install(
  FILES  "${CMAKE_CURRENT_BINARY_DIR}/cmake/Sqlpp-connector-postgresqlConfig.cmake"
  DESTINATION ${ConfigPackageLocation}
  COMPONENT Devel
)
