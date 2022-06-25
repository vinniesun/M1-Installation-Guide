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