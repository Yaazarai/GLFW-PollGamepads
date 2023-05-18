# GLFW-PollGamepads (C/C++)
Creates a polling function and associated Button/Axis/Trigger changed callbacks for gamepads to be called alongside glfwPollEvents. Axis/Trigger changed callbacks only invoke on changes with float precision of 3 decimal places. The axis changed callback invokes once for both the X-axis and Y-axis if either or both the left or right gamepad axis changed. The API will automatically map everything to Xbox-like controllers (see GLFW API for glfwGetGamepadState).

The Button/Axis/Trigger callbacks can be set with the following functions:
```C
glfwSetGamepadButtonCallback(callback);
glfwSetGamepadAxisCallback(callback);
glfwSetGamepadTriggerCallback(callback);
```
These callbacks take the form of:
```C
typedef void (*GLFWgamepadbuttonfun)(const GLFWgamepadstate* gamepad, int32_t gpad, int32_t button, int32_t action);
typedef void (*GLFWgamepadaxisfun)(const GLFWgamepadstate* gamepad, int32_t gpad, int32_t axisXID, float_t axisX, int32_t axisYID, float_t axisY);
typedef void (*GLFWgamepadtriggerfun)(const GLFWgamepadstate* gamepad, int32_t gpad, int32_t axisXID, float_t axis);

// Example Callbacks:
void GamepadButtonChanged(const GLFWgamepadstate* gamepad, int32_t gpad, int32_t button, int32_t action) { ... }
void GamepadAxisChanged(const GLFWgamepadstate* gamepad, int32_t gpad, int32_t axisXID, float_t axisX, int32_t axisYID, float_t axisY) { ... }
void GamepadTriggerChanged(const GLFWgamepadstate* gamepad, int32_t gpad, int32_t axisXID, float_t axis) { ... }

glfwSetGamepadButtonCallback(GamepadButtonChanged);
glfwSetGamepadAxisCallback(GamepadAxisChanged);
glfwSetGamepadTriggerCallback(GamepadTriggerChanged);
```
Call this polling function in your GLFW window's main loop to process gamepad evnets:
```C
glfwPollGamepads();
```
