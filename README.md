# GLFW-PollGamepads (C/C++)
Creates a polling function and associated Button/Axis/Trigger changed callbacks for gamepads to be called alongside glfwPollEvents. Axis/Trigger changed callbacks only invoke on changes with float precision of 3 decimal places. The axis changed callback invokes once for both the X-axis and Y-axis if either or both the left or right gamepad axis changed. The API will automatically map everything to Xbox-like controllers (see GLFW API for glfwGetGamepadState).

Callback parameter formad:
```C
glfwGamepadButtonCallback(const* gpad_state, gpad_id, button_id, button_state)
glfwGamepadAxisCallback(const* gpad_state, gpad_id, axisL_id, axisL_state, axisR_id, axisR_state)
glfwGamepadTriggerCallback(const* gpad_state, gpad_id, axis_id, axis_state)
```

Gamepad Polling Header:
```C
#ifndef GLFW_GAMEPAD_POLLING_HEADER
#ifdef GLFW_GAMEPAD_POLLING_HEADER
#ifdef __cplusplus
extern "C" {
#endif

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

#ifdef __cplusplus
}
#endif
#endif
```
