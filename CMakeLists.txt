PROJECT(DIFODO)

CMAKE_MINIMUM_REQUIRED(VERSION 2.4)
if(COMMAND cmake_policy)
      cmake_policy(SET CMP0003 NEW)  # Required by CMake 2.7+
endif(COMMAND cmake_policy)

# custom cmake modules
LIST(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake_modules)

#Find dependencies (Eigen is included in MRPT)
FIND_PACKAGE(MRPT REQUIRED base gui opengl obs)
FIND_PACKAGE(OpenCV REQUIRED)
FIND_PACKAGE(OpenNI2 REQUIRED)

INCLUDE_DIRECTORIES(${PROJECT_SOURCE_DIR})
INCLUDE_DIRECTORIES(${OpenNI2_INCLUDE_DIRS})


ADD_LIBRARY(difodo_lib
	CDifodo.h
	DifOdometry_Camera.h
	DifOdometry_Datasets.h
	CDifodo.cpp
	DifOdometry_Camera.cpp
	DifOdometry_Datasets.cpp	
	)
	
TARGET_LINK_LIBRARIES(difodo_lib
	${MRPT_LIBS}
	${OpenNI2_LIBRARY}
	${OpenCV_LIBS})
	
		
#To run online with an RGB-D camera	
ADD_EXECUTABLE(Difodo-Camera 	DifOdometry_Camera_main.cpp)	
TARGET_LINK_LIBRARIES(Difodo-Camera 	difodo_lib)

		
#To test it with the TUM dataset		
ADD_EXECUTABLE(Difodo-Datasets 	DifOdometry_Datasets_main.cpp)
TARGET_LINK_LIBRARIES(Difodo-Datasets 	difodo_lib)
		
		

# Set optimized building:
IF(CMAKE_COMPILER_IS_GNUCXX)
	SET(CMAKE_BUILD_TYPE "Release") #I'm not sure if this does anything
	SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O3 -mtune=native -mavx")
ENDIF(CMAKE_COMPILER_IS_GNUCXX)

