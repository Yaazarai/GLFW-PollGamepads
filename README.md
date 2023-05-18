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
GLFWAPI GLFWgamepadstate glfwGamepadCache[GLFW_JOYSTICK_LAST + 1];
GLFWAPI float_t glfwRoundfd(float_t value, float_t precision) { return std::round(value / precision) * precision; }
GLFWAPI void glfwPollGamepads() {
	for (int32_t i = 0; i <= GLFW_JOYSTICK_LAST; i++) {
		if (!glfwJoystickPresent(i)) continue;

		GLFWgamepadstate gpstate;
		glfwGetGamepadState(i, &gpstate);

		for(int32_t b = 0; b <= GLFW_GAMEPAD_BUTTON_LAST; b++) {
			if (gpstate.buttons[b] != glfwGamepadCache[b].buttons[b]) {
				glfwGamepadCache[b].buttons[b] = gpstate.buttons[b];
				glfwGamepadButtonCallback(&gpstate, i, b, gpstate.buttons[b]);
			}
		}

		if (glfwRoundfd(gpstate.axes[GLFW_GAMEPAD_AXIS_LEFT_X], 0.0001) != glfwRoundfd(glfwGamepadCache->axes[GLFW_GAMEPAD_AXIS_LEFT_X], 0.0001)
			|| glfwRoundfd(gpstate.axes[GLFW_GAMEPAD_AXIS_LEFT_Y], 0.0001) != glfwRoundfd(glfwGamepadCache->axes[GLFW_GAMEPAD_AXIS_LEFT_Y], 0.0001)) {
			glfwGamepadCache->axes[GLFW_GAMEPAD_AXIS_LEFT_X] = gpstate.axes[GLFW_GAMEPAD_AXIS_LEFT_X];
			glfwGamepadCache->axes[GLFW_GAMEPAD_AXIS_LEFT_Y] = gpstate.axes[GLFW_GAMEPAD_AXIS_LEFT_Y];
			glfwGamepadAxisCallback(&gpstate, i, GLFW_GAMEPAD_AXIS_LEFT_X, gpstate.axes[GLFW_GAMEPAD_AXIS_LEFT_X], GLFW_GAMEPAD_AXIS_LEFT_Y, gpstate.axes[GLFW_GAMEPAD_AXIS_LEFT_Y]);
		}

		if (glfwRoundfd(gpstate.axes[GLFW_GAMEPAD_AXIS_RIGHT_X], 0.0001) != glfwRoundfd(glfwGamepadCache->axes[GLFW_GAMEPAD_AXIS_RIGHT_X], 0.0001)
			|| glfwRoundfd(gpstate.axes[GLFW_GAMEPAD_AXIS_RIGHT_Y], 0.0001) != glfwRoundfd(glfwGamepadCache->axes[GLFW_GAMEPAD_AXIS_RIGHT_Y], 0.0001)) {
			glfwGamepadCache->axes[GLFW_GAMEPAD_AXIS_RIGHT_X] = gpstate.axes[GLFW_GAMEPAD_AXIS_RIGHT_X];
			glfwGamepadCache->axes[GLFW_GAMEPAD_AXIS_RIGHT_Y] = gpstate.axes[GLFW_GAMEPAD_AXIS_RIGHT_Y];
			glfwGamepadAxisCallback(&gpstate, i, GLFW_GAMEPAD_AXIS_RIGHT_X, gpstate.axes[GLFW_GAMEPAD_AXIS_RIGHT_X], GLFW_GAMEPAD_AXIS_RIGHT_Y, gpstate.axes[GLFW_GAMEPAD_AXIS_RIGHT_Y]);
		}

		if (glfwRoundfd(gpstate.axes[GLFW_GAMEPAD_AXIS_LEFT_TRIGGER], 0.0001) != glfwRoundfd(glfwGamepadCache->axes[GLFW_GAMEPAD_AXIS_LEFT_TRIGGER], 0.0001)) {
			glfwGamepadCache->axes[GLFW_GAMEPAD_AXIS_LEFT_TRIGGER] = gpstate.axes[GLFW_GAMEPAD_AXIS_LEFT_TRIGGER];
			glfwGamepadTriggerCallback(&gpstate, i, GLFW_GAMEPAD_AXIS_LEFT_TRIGGER, gpstate.axes[GLFW_GAMEPAD_AXIS_LEFT_TRIGGER]);
		}

		if (glfwRoundfd(gpstate.axes[GLFW_GAMEPAD_AXIS_RIGHT_TRIGGER], 0.0001) != glfwRoundfd(glfwGamepadCache->axes[GLFW_GAMEPAD_AXIS_RIGHT_TRIGGER], 0.0001)) {
			glfwGamepadCache->axes[GLFW_GAMEPAD_AXIS_RIGHT_TRIGGER] = gpstate.axes[GLFW_GAMEPAD_AXIS_RIGHT_TRIGGER];
			glfwGamepadTriggerCallback(&gpstate, i, GLFW_GAMEPAD_AXIS_RIGHT_TRIGGER, gpstate.axes[GLFW_GAMEPAD_AXIS_RIGHT_TRIGGER]);
		}
	}
}
```
