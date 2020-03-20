# Overview
This project is an open-source tool for composing multiple, customized touch screen joysticks with the objective of improving the tooling available to Roblox developers for mobile games.

# API documentation
*This API documentation is a WIP and subject to change. For now, this is intended as the goal API.*

## Viewport regions

### IViewportRegion
An interface for defining viewport regions so as to check if a given point exists in the region\
Intended to be implemented for developers who may need a more specific implementation

#### Instance methods
`isPointInRegion(point: Vector2): boolean`\
Determines whether the given point exists in the region
##### Parameters
- point\
The viewport point in question\
Units should be in pixels
##### Returns
Returns a value indicating whether the given point is in the region

### RectangleViewportRegion
An implementation of [`IViewportRegion`](#IViewportRegion) that describes a rectangular region of the viewport

#### Constructor
`new RectangleViewportRegion(topLeft: Vector2, bottomRight: Vector2)`
##### Parameters
- topLeft\
A `Vector2` describing the top-left point in the viewport of the region
- bottomRight\
A `Vector2` describing the bottom-right point in the viewport of the region

### CircleViewportRegion
An implementation of [`IViewportRegion`](#IViewportRegion) that describes a circular region of the viewport

#### Constructor
`new CircleViewportRegion(centerPoint: Vector2, radiusInPixels: number)`
##### Parameters
- centerPoint\
A `Vector2` describing the center point in the viewport of the region
- radiusInPixels\
A `number` describing the radius in pixels of the region

## Renderers

### IRenderer
An interface for renderers for both the gutter and thumb\
Intended to be implemented for developers who may need a more specific implementation

#### Instance methods
`destroy(): void`\
Destroys the instance, making it incapable of rendering later and cleaning up all of its related instances and signals

`hide(): void`\
Hides any rendered instances

`render(absoluteCenter: Vector2, radiusInPixels: number): void`\
Renders on the screen
##### Parameters
- absoluteCenter\
A `Vector2` describing the absolute center of the renderer in the viewport
- radiusInPixels\
A `number` describing the radius in pixels to render from the given center

### FilledCircleRenderer
An implementation of [`IRenderer`](#IRenderer) that renders a solid color, filled circle

#### Constructor
`new FilledCircleRenderer(color: Color3, transparency?: number)`
##### Parameters
- color\
A `Color3` that describes the color of the filled circle
- transparency\
A `number` in the range [0, 1] that describes the transparency of the filled circle

## Joystick API

### IJoystickConfiguration
The interface for constructing a new [`Joystick`](#Joystick)

#### Fields
- activationRegion: [`IViewportRegion`](#IViewportRegion)
Defines the screen region in which the [`Joystick`](#Joystick) instance can be activated by the player's input
- renderableRegion?: [`IViewportRegion`](#IViewportRegion)\
Defines the screen region in which the [`Joystick`](#Joystick) instance can be rendered\
Defaults to the entire viewport
- gutterRenderer: [`IRenderer`](#IRenderer)\
Defines the renderer instance for the gutter of the [`Joystick`](#Joystick) instance
- thumbRenderer: [`IRenderer`](#IRenderer)\
Defines the renderer instance for the thumb of the [`Joystick`](#Joystick) instance
- inactiveCenterPoint: `Vector2`\
Defines the inactive center point in the viewport of the [`Joystick`](#Joystick) instance
- priorityLevel?: `number`\
Defines the priority leve lof the [`Joystick`](#Joystick) instance\
Defaults to 1
- initializedEnabled?: `boolean`\
Defines whether the [`Joystick`](#Joystick) instance should initialize as enabled\
Defaults to false
- initializedVisible?: `boolean`\
Defines whether the [`Joystick`](#Joystick) instance should initialize as visible\
Defaults to false

### Joystick

#### Constructor
`new Joystick(configuration: IJoystickConfiguration)`
##### Parameters
- configuration\
An [`IJoystickConfiguration`](#IJoystickConfiguration) that defines the configuration parameters for the new [`Joystick`](#Joystick) instance

#### Instance members
`readonly input?: Vector2`\
A readonly, optional field that reports the current input for the instance, if any\
When defined, this field will be a `Vector2` where each component will be in the range [0, 1] and the magnitude will never exceed 1 describing the position in the unit circle defined by the gutter of the instance\
This field will be `undefined` when `isActive` is false and will always be defined when `isActive` is true

`readonly isActive: boolean`\
A readonly field for determining whether the instance is active\
When active, the joystick is actively being interacted with by the player

`readonly isEnabled: boolean`\
A readonly field for determining whether the instance is enabled\
When enabled and visible, the joystick can be interacted with by the player

`readonly isVisible: boolean`\
A readonly field for determining whether the instance is visible\
When visible, the joystick will be rendered

`readonly priorityLevel: number`\
A readonly field for determining the priority level for the instance

#### Instance signals
`readonly IReadOnlySignal<(initialInput: Vector2) => void> activated`\
Fired when the instance is activated
##### Arguments
- initialInput\
A `Vector2` describing the initial input from the player upon activation

`readonly IReadOnlySignal<(finalInput: Vector2) => void> deactivated`\
Fired when the instance is deactivated
##### Arguments
- finalInput\
A `Vector2` describing the final input from the player just prior to deactivation

`readonly IReadOnlySignal disabled`\
Fired when the instance is disabled

`readonly IReadOnlySignal enabled`\
Fired when the instance is enabled

`readonly IReadOnlySignal<(newInput: Vector2) => void> inputChanged`\
Fired when the instance's input has changed
##### Arguments
- newInput\
A `Vector2` describing the new input from the player

`readonly IReadOnlySignal<(newValue: boolean) => void> visibilityChanged`\
Fired when the instance's visibility has changed
##### Arguments
- newValue\
A `boolean` indicating whether the instance is now visible

#### Instance methods
`void destroy()`\
Destroys the instance, its renderers, any other associated instances, and disconnects all connections to its signals\
An instance cannot be re-used after being destroyed

`void setActivationRegion(newRegion: IViewportRegion)`\
Sets the activation region of the instance\
##### Parameters
- newRegion\
An [`IViewportRegion`](#IViewportRegion) to use as the new activation region for the instance

`void setEnabled(newValue: boolean)`\
Sets the enabled state of the instance to the given newValue\
If the new value does not match the current state, then the appropriate signal (either `enabled` or `disabled`) will fire
##### Parameters
- newValue\
A `boolean` indicating whether the instance should be enabled

`void setGutterRenderer(newRenderer: IRenderer)`\
Sets the gutter renderer for the instance
#### Parameters
- newRenderer\
An [`IRenderer`](#IRenderer) to use as the new gutter renderer for the instance

`void setInactiveCenterPoint(newPoint: Vector2)`\
Sets the inactive center point for the instance
#### Parameters
- newPoint\
A `Vector2` to use as the new inactive center point for the instance

`void setPriorityLevel(newPriorityLevel: number)`\
Sets the priority level for the instance
#### Parameters
- newPriorityLevel\
A `number` to use as the new priority level for the instance

`void setRenderableRegion(newRegion: IViewportRegion)`\
Sets the renderable region of the instance\
##### Parameters
- newRegion\
An [`IViewportRegion`](#IViewportRegion) to use as the new renderable region for the instance

`void setThumbRenderer(newRenderer: IRenderer)`\
Sets the thumb renderer for the instance
#### Parameters
- newRenderer\
An [`IRenderer`](#IRenderer) to use as the new thumb renderer for the instance

`void setVisible(newValue: boolean)`\
Sets the visible state of the instance to the given newValue\
If the new value does not match the current state, then the `visibilityChanged` signal will fire
##### Parameters
- newValue\
A `boolean` indicating whether the instance should be visible