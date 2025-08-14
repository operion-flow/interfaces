# Operion Interfaces

This package defines the core interfaces and contracts for building pluggable actions and triggers in the Operion workflow system.

## Overview

The `interfaces` package provides the foundational interfaces that allow developers to create custom actions and triggers that can be dynamically loaded and executed within Operion workflows.

## Interfaces

### Action Interface

The `Action` interface defines the contract for workflow actions:

```go
type Action interface {
    Execute(ctx context.Context, executionCtx models.ExecutionContext, logger *slog.Logger) (any, error)
    Validate(ctx context.Context) error
}
```

- **Execute**: Performs the action with the provided execution context
- **Validate**: Validates the action configuration before execution

### ActionFactory Interface

The `ActionFactory` interface defines how action plugins are created and configured:

```go
type ActionFactory interface {
    Create(ctx context.Context, config map[string]any) (Action, error)
    ID() string
    Name() string
    Description() string
    Schema() map[string]any
}
```

- **Create**: Creates a new action instance with the given configuration
- **ID**: Returns a unique identifier for the action type
- **Name**: Returns a human-readable name for the action
- **Description**: Returns a description of what the action does
- **Schema**: Returns the JSON schema for action configuration

### Trigger Interface

The `Trigger` interface defines the contract for workflow triggers:

```go
type Trigger interface {
    Start(ctx context.Context, callback TriggerCallback) error
    Stop(ctx context.Context) error
    Validate(ctx context.Context) error
}
```

- **Start**: Starts the trigger and calls the callback when events occur
- **Stop**: Stops the trigger and cleans up resources
- **Validate**: Validates the trigger configuration

### TriggerFactory Interface

The `TriggerFactory` interface defines how trigger plugins are created:

```go
type TriggerFactory interface {
    Create(ctx context.Context, config map[string]any, logger *slog.Logger) (Trigger, error)
    ID() string
    Name() string
    Description() string
    Schema() map[string]any
}
```

## Plugin Development

### Creating an Action Plugin

To create a custom action plugin:

1. Implement the `Action` interface
2. Implement the `ActionFactory` interface
3. Export the factory as a variable named `Action`

Example:

```go
package main

import (
    "context"
    "log/slog"
    "github.com/dukex/operion/pkg/models"
    "github.com/operion-flow/interfaces"
)

type MyAction struct {
    config map[string]any
}

func (a *MyAction) Execute(ctx context.Context, ectx models.ExecutionContext, logger *slog.Logger) (any, error) {
    // Implementation here
    return nil, nil
}

func (a *MyAction) Validate(ctx context.Context) error {
    // Validation logic here
    return nil
}

type MyActionFactory struct{}

func (f MyActionFactory) Create(ctx context.Context, config map[string]any) (interfaces.Action, error) {
    return &MyAction{config: config}, nil
}

func (f MyActionFactory) ID() string { return "my-action" }
func (f MyActionFactory) Name() string { return "My Action" }
func (f MyActionFactory) Description() string { return "A custom action" }
func (f MyActionFactory) Schema() map[string]any { return map[string]any{} }

var Action = MyActionFactory{}
```

### Creating a Trigger Plugin

To create a custom trigger plugin:

1. Implement the `Trigger` interface
2. Implement the `TriggerFactory` interface
3. Export the factory as a variable named `Trigger`

## Usage

This package is meant to be imported by:

- Plugin developers creating custom actions and triggers
- The main Operion system for loading and managing plugins
- Testing frameworks for validating plugin implementations

## Module Information

- **Module**: `github.com/operion-flow/interfaces`
- **Go Version**: 1.24.4

## Related Projects

- [Operion](https://github.com/dukex/operion) - Main workflow engine
- [Operion Docs](https://github.com/dukex/operion-docs) - Documentation and examples

## License

This project is licensed under the same terms as the main Operion project.