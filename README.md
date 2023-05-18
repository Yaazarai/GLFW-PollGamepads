# GLFW-PollGamepads (C/C++)
Creates a polling callback for gamepads to be called alongside glfwPollEvents.
```C
typedef void (*GLFWgamepadbuttonfun)(const GLFWgamepadstate* gamepad, int32_t gpad, int32_t button, int32_t action);
typedef void (*GLFWgamepadaxisfun)(const GLFWgamepadstate* gamepad, int32_t gpad, int32_t axisXID, float_t axisX, int32_t axisYID, float_t axisY);
typedef void (*GLFWgamepadtriggerfun)(const GLFWgamepadstate* gamepad, int32_t gpad, int32_t axisXID, float_t axis);
GLFWAPI GLFWgamepadbuttonfun glfwGamepadButtonCallback = NULL;
GLFWAPI GLFWgamepadaxisfun glfwGamepadAxisCallback = NULL;
GLFWAPI GLFWgamepadtriggerfun glfwGamepadTriggerCallback = NULL;
GLFWAPI void glfwSetGamepadButtonCallback(GLFWgamepadbuttonfun callback) { glfwGamepadButtonCallback = callback; }
GLFWAPI void glfwSetGamepadAxisCallback(GLFWgamepadaxisfun callback) { glfwGamepadAxisCallback = callback; }
GLFWAPI void glfwSetGamepadTriggerCallback(GLFWgamepadtriggerfun callback) { glfwGamepadTriggerCallback = callback; }
GLFWAPI GLFWgamepadstate glfwGamepadCache[GLFW_JOYSTICK_LAST + 1]{};
GLFWAPI float_t glfwRoundfd(float_t value, float_t precision) { return std::round(value * precision) / precision; }
GLFWAPI const char* glfwDefaultMapping = "78696e70757401000000000000000000,XInput Gamepad (GLFW),platform:Windows,a:b0,b:b1, x : b2, y : b3, leftshoulder : b4, rightshoulder : b5, back : b6, start : b7, leftstick : b8,rightstick : b9, leftx : a0, lefty : a1, rightx : a2, righty : a3, lefttrigger : a4,righttrigger : a5, dpup : h0.1, dpright : h0.2, dpdown : h0.4, dpleft : h0.8,";
GLFWAPI void glfwSetDefaultMappingXbox() { glfwUpdateGamepadMappings(glfwDefaultMapping); }
GLFWAPI void glfwPollGamepads() {
	for (int32_t i = 0; i <= GLFW_JOYSTICK_LAST; i++) {
		if (glfwJoystickPresent(i) == GLFW_FALSE) continue;

		GLFWgamepadstate gpstate;
		glfwGetGamepadState(i, &gpstate);

		for(int32_t b = 0; b <= GLFW_GAMEPAD_BUTTON_LAST; b++) {
			if (gpstate.buttons[b] != glfwGamepadCache[i].buttons[b]) {
				glfwGamepadCache[i].buttons[b] = gpstate.buttons[b];
				glfwGamepadButtonCallback(&gpstate, i, b, gpstate.buttons[b]);
			}
		}

		if (glfwRoundfd(gpstate.axes[GLFW_GAMEPAD_AXIS_LEFT_X], 1000) != glfwRoundfd(glfwGamepadCache[i].axes[GLFW_GAMEPAD_AXIS_LEFT_X], 1000)
			|| glfwRoundfd(gpstate.axes[GLFW_GAMEPAD_AXIS_LEFT_Y], 1000) != glfwRoundfd(glfwGamepadCache[i].axes[GLFW_GAMEPAD_AXIS_LEFT_Y], 1000)) {
			glfwGamepadCache[i].axes[GLFW_GAMEPAD_AXIS_LEFT_X] = gpstate.axes[GLFW_GAMEPAD_AXIS_LEFT_X];
			glfwGamepadCache[i].axes[GLFW_GAMEPAD_AXIS_LEFT_Y] = gpstate.axes[GLFW_GAMEPAD_AXIS_LEFT_Y];

			if (glfwGamepadAxisCallback != NULL)
				glfwGamepadAxisCallback(&gpstate, i, GLFW_GAMEPAD_AXIS_LEFT_X, gpstate.axes[GLFW_GAMEPAD_AXIS_LEFT_X], GLFW_GAMEPAD_AXIS_LEFT_Y, gpstate.axes[GLFW_GAMEPAD_AXIS_LEFT_Y]);
		}

		if (glfwRoundfd(gpstate.axes[GLFW_GAMEPAD_AXIS_RIGHT_X], 1000) != glfwRoundfd(glfwGamepadCache[i].axes[GLFW_GAMEPAD_AXIS_RIGHT_X], 1000)
			|| glfwRoundfd(gpstate.axes[GLFW_GAMEPAD_AXIS_RIGHT_Y], 1000) != glfwRoundfd(glfwGamepadCache[i].axes[GLFW_GAMEPAD_AXIS_RIGHT_Y], 1000)) {
			glfwGamepadCache[i].axes[GLFW_GAMEPAD_AXIS_RIGHT_X] = gpstate.axes[GLFW_GAMEPAD_AXIS_RIGHT_X];
			glfwGamepadCache[i].axes[GLFW_GAMEPAD_AXIS_RIGHT_Y] = gpstate.axes[GLFW_GAMEPAD_AXIS_RIGHT_Y];
				
			if (glfwGamepadAxisCallback != NULL)
				glfwGamepadAxisCallback(&gpstate, i, GLFW_GAMEPAD_AXIS_RIGHT_X, gpstate.axes[GLFW_GAMEPAD_AXIS_RIGHT_X], GLFW_GAMEPAD_AXIS_RIGHT_Y, gpstate.axes[GLFW_GAMEPAD_AXIS_RIGHT_Y]);
		}

		if (glfwRoundfd(gpstate.axes[GLFW_GAMEPAD_AXIS_LEFT_TRIGGER], 1000) != glfwRoundfd(glfwGamepadCache[i].axes[GLFW_GAMEPAD_AXIS_LEFT_TRIGGER], 1000)) {
			glfwGamepadCache[i].axes[GLFW_GAMEPAD_AXIS_LEFT_TRIGGER] = gpstate.axes[GLFW_GAMEPAD_AXIS_LEFT_TRIGGER];
				
			if (glfwGamepadTriggerCallback != NULL)
				glfwGamepadTriggerCallback(&gpstate, i, GLFW_GAMEPAD_AXIS_LEFT_TRIGGER, gpstate.axes[GLFW_GAMEPAD_AXIS_LEFT_TRIGGER]);
		}

		if (glfwRoundfd(gpstate.axes[GLFW_GAMEPAD_AXIS_RIGHT_TRIGGER], 1000) != glfwRoundfd(glfwGamepadCache[i].axes[GLFW_GAMEPAD_AXIS_RIGHT_TRIGGER], 1000)) {
			glfwGamepadCache[i].axes[GLFW_GAMEPAD_AXIS_RIGHT_TRIGGER] = gpstate.axes[GLFW_GAMEPAD_AXIS_RIGHT_TRIGGER];
				
			if (glfwGamepadTriggerCallback != NULL)
				glfwGamepadTriggerCallback(&gpstate, i, GLFW_GAMEPAD_AXIS_RIGHT_TRIGGER, gpstate.axes[GLFW_GAMEPAD_AXIS_RIGHT_TRIGGER]);
		}
	}
}
```
