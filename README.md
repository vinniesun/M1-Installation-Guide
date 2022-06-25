# M1-Installation-Guide

This repository details how to install the following packages on M1 Macs:
* OpenCV
* OpenGL (GLFW3)


## OpenGL Installation
To install OpenGL (GLFW) on the M1 Macs, go to Terminal and follow the steps below:
* Open Terminal
* Go to the folder that will store the GLFW files
* In Terminal, enter the following commands:
``` shell
git clone https://github.com/glfw/glfw.git && \
cd glfw && \
cmake -DCMAKE_OSX_ARCHITECTURES=arm64 . && \
make && \
sudo make install
```
* Test if the installation was successful by creating a C++ file called test.cpp, and enter the following code into the file:
```C++ title="test.cpp"
#include <GLFW/glfw3.h>
#include <iostream>

const size_t WIDTH = 640;
const size_t HEIGHT = 480;
const char* WINDOW_NAME = "Test OpenGL";

/*
 * Callback to handle the "close window" event, once the user pressed the Escape key.
 */
static void quit_callback(GLFWwindow *window, int key, int scancode, int action, int _mods)
{
    if (key == GLFW_KEY_ESCAPE && action == GLFW_PRESS)
        glfwSetWindowShouldClose(window, GLFW_TRUE);
}

int main(void)
{
    GLFWwindow *window;

    if (!glfwInit()) {
        std::cerr << "ERROR: could not start GLFW3" << std::endl;
        return -1; // Initialize the lib
    }

    // Minimum target is OpenGL 4.1
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 4);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 1);
    window = glfwCreateWindow(HEIGHT, WIDTH, WINDOW_NAME, NULL, NULL);
    if (!window)
    {
        std::cerr << "ERROR: could not open window with GLFW3" << std::endl;
        glfwTerminate();
        return -1;
    }
    // Close the window as soon as the Escape key has been pressed
    glfwSetKeyCallback(window, quit_callback);
    // Makes the window context current
    glfwMakeContextCurrent(window);

    const GLubyte* renderer = glGetString(GL_RENDERER);
    const GLubyte* version = glGetString(GL_VERSION);
    std::cout << "Renderer: " << renderer << std::endl;
    std::cout << "OpenGL version supported: " << version << std::endl;

    // Now we have a current OpenGL context, we can use OpenGL normally
    while (!glfwWindowShouldClose(window))
    {
        // Render
        glClear(GL_COLOR_BUFFER_BIT);
        // Swap front and back buffers
        glfwSwapBuffers(window);
        // Poll for and process events
        glfwPollEvents();
    }

    // ... here, the user closed the window
    glfwTerminate();
    return 0;
}
```
* Compile test.cpp with the following command:
``` shell
g++ test.cpp -o test -std=c++17 -framework Cocoa -framework OpenGL -framework IOKit -lglfw3
```
* Launch the code with the following command:
```shell
./test
```
* We should see the following output message in terminal:
```shell
Renderer: Apple M1
OpenGL version supported: 4.1 Metal - 76.3
```

## OpenCV 4.5.0 Installation
To install OpenCV on the M1 Macs, you'll have to jump through a few hurdles, compared to the Intel Macs.
First let's take a look at installing OpenCV for C++ (Need to figure out how to make symbolic link to Python's Virtual Environment)
Follow the steps below:
* Launch Terminal
* Create your Python3 virtual environment:
```shell
python3 -m venv /path/to/python-venv
```
* Activate virtual environment:
```shell
cd /path/to/python-venv/bin
source activate
```
* Install NumPy in Virtual Environment
```Shell
pip install numpy
```
* If you haven't installed Homebrew, the Mac package manager, enter the following command in Terminal:
```shell
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
* Use Homebrew to install wget:
```shell
brew install wget
```
* Change to the directory that will be used to store the OpenCV source file
* Download OpenCV 4.5.0 and OpenCV-Contrib 4.5.0
```shell
wget -O opencv.zip https://github.com/opencv/opencv/archive/4.5.0.zip
wget -O opencv_contrib.zip https://github.com/opencv/opencv_contrib/archive/4.5.0.zip
```
* Unzip the two zip files
```shell
unzip opencv.zip
unzip opencv_contrib.zip
```
* Change into the OpenCV directory and create build directory
```shell
cd opencv-4.5.0
mkdir build && cd build
```
* Call Cmake to build OpenCV
```shell
cmake \
  -DCMAKE_SYSTEM_PROCESSOR=arm64 \
  -DCMAKE_OSX_ARCHITECTURES=arm64 \
  -DWITH_OPENJPEG=OFF \
  -DWITH_IPP=OFF \
  -D CMAKE_BUILD_TYPE=RELEASE \
  -D CMAKE_INSTALL_PREFIX=/usr/local \
  -D OPENCV_EXTRA_MODULES_PATH=/Users/vincent/Desktop/opencv/opencv_contrib-4.5.0/modules \
  -D PYTHON3_EXECUTABLE=/Users/vincent/Desktop/python-venv/bin/python3 \
  -D BUILD_opencv_python2=OFF \
  -D BUILD_opencv_python3=ON \
  -D INSTALL_PYTHON_EXAMPLES=ON \
  -D INSTALL_C_EXAMPLES=OFF \
  -D OPENCV_ENABLE_NONFREE=ON \
  -D BUILD_EXAMPLES=ON ..
```
* After building successfully, call the following command in Terminal:
```shell
make -j8
```
* Install OpenCV with the command below:
```shell
sudo make install
```
* Now let's test to make sure the installation was a success. go to one of the sample C++ code in OpenCV-4.5.0 folder
```shell
cd ~/Desktop/opencv/opencv-4.5.0/samples/cpp
```
* Compile the opencv_version.cpp file with the following command:
```shell
g++ -std=c++14 -ggdb opencv_version.cpp -o opencv_version -I/usr/local/include/opencv4/ -lopencv_core
```
* Launch the compiled binary with the following command:
```shell
./opencv_version
```
* We should see the Terminal prompt printing out the installed OpenCV version.
* While we are still in the Virtual Environment, we can install the OpenCV package with the following command:
```shell
arch -arm64 python3.9 -m pip install opencv-python
```
* We can make sure it works by entering the following command:
```shell
python3
```
```py
import cv2
cv2.__version__
```
* This should print out the installed OpenCV version.