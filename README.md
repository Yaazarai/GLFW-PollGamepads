# GLFW-PollGamepads (C/C++)
Creates a polling callback for gamepads to be called alongside glfwPollEvents.
```C
struct glfwGamepad { int32_t id; uint8_t buttons[GLFW_JOYSTICK_LAST + 1]; float axis[GLFW_JOYSTICK_LAST + 1]; };
typedef void (*GLFWgamepadbuttonfun)(int32_t gamepad, int32_t button, int32_t action);
typedef void (*GLFWgamepadaxisfun)(int32_t gamepad, int32_t axis, float_t axisX, float_t axisY);
inline static GLFWgamepadbuttonfun glfwGamepadButtonCallback = NULL;
inline static GLFWgamepadaxisfun glfwGamepadAxisCallback = NULL;
inline static void glfwSetGamepadButtonCallback(GLFWgamepadbuttonfun callback) { glfwGamepadButtonCallback = callback; }
inline static void glfwSetGamepadAxisCallback(GLFWgamepadaxisfun callback) { glfwGamepadAxisCallback = callback; }
inline static glfwGamepad glfwGamepadCache[GLFW_JOYSTICK_LAST + 1];
inline static float_t roundfd(float_t value, float_t precision) { return std::round(value / precision) * precision; }
inline static void glfwPollGamepads() {
	for (int32_t i = 0; i <= GLFW_JOYSTICK_LAST; i++) {
		if (!glfwJoystickPresent(i)) continue;

		int32_t axis_count, button_count;
		const float_t* gpad_axis = glfwGetJoystickAxes(static_cast<int>(i), &axis_count);
		const uint8_t* gpad_buttons = glfwGetJoystickButtons(static_cast<int32_t>(i), &button_count);
				
		glfwGamepad& gpadCache = glfwGamepadCache[i];

		int32_t button_check = (button_count < GLFW_JOYSTICK_LAST + 1) ? button_count : GLFW_JOYSTICK_LAST + 1;
		for (int32_t j = 0; j < button_check; j++) {
			if (gpadCache.buttons[j] != gpad_buttons[j]) {
				gpadCache.buttons[j] = gpad_buttons[j];

				if (glfwGamepadButtonCallback != NULL)
					glfwGamepadButtonCallback(i, j, gpad_buttons[j]);
			}
		}
				
		int32_t axis_check = (axis_count < GLFW_JOYSTICK_LAST + 1)? axis_count : GLFW_JOYSTICK_LAST + 1;
		for (int32_t j = 0; j < axis_check; j += 2) {
			float_t axisX = roundfd(gpad_axis[j], 0.0001);
			float_t axisY = roundfd(gpad_axis[j + 1], 0.0001);

			if (roundfd(gpadCache.axis[j], 0.0001) != axisX || roundfd(gpadCache.axis[j + 1], 0.0001) != axisY) {
				gpadCache.axis[j] = axisX;
				gpadCache.axis[j + 1] = axisY;

				if (glfwGamepadAxisCallback != NULL)
					glfwGamepadAxisCallback(i, j, axisX, axisY);
			}
		}
	}
}
```
