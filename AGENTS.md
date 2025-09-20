# AGENTS.md

This document provides detailed guidelines for AI agents working on the KOReader codebase. Adhering to these guidelines is crucial for generating code that is consistent with the project's architecture and style.

## 1. Project Architecture

The KOReader codebase is organized into three main parts:

*   `frontend/`: Contains the user interface and application logic, written almost entirely in **Lua**. This is where most development happens.
*   `base/`: The core backend, containing document parsers (e.g., for PDF, DjVu) and rendering libraries, written in **C and C++**.
*   `plugins/`: Self-contained extensions to the application, written in Lua.

**Your primary focus will likely be on the `frontend/` directory.**

## 2. Development Environment & Workflow

### Setup

The recommended development environment is the official Docker image, which provides a consistent setup with all dependencies included.

1.  **Clone the repository**:
    ```bash
    git clone https://github.com/koreader/koreader.git
    cd koreader
    ```
2.  **Fetch third-party dependencies**:
    ```bash
    ./kodev fetch-thirdparty
    ```

### Building and Running

The `./kodev` script is your main tool for building and running the application.

*   **Build the emulator**: `./kodev build`
*   **Run the emulator**: `./kodev run`

## 3. Lua Development Guidelines

### 3.1. Module System

The project uses a standard Lua module pattern.

*   **Defining a module:** A file defines a table and returns it at the end.
    ```lua
    -- my_module.lua
    local MyModule = {}

    function MyModule.say_hello()
        print("Hello, World!")
    end

    return MyModule
    ```
*   **Using a module:** Use the `require()` function with a path relative to the project root.
    ```lua
    -- another_file.lua
    local MyModule = require("path/to/my_module")

    MyModule.say_hello()
    ```

### 3.2. Object-Oriented Programming

KOReader uses a prototype-based OOP style with metatables. **Do not use class libraries or other OOP implementations.** Follow the existing pattern.

*   **Inheritance:** The `:extend()` method is used to create a "subclass" from a "base class".
    ```lua
    -- base_widget.lua
    local BaseWidget = {}
    function BaseWidget:extend(subclass_prototype)
        local o = subclass_prototype or {}
        setmetatable(o, self)
        self.__index = self
        return o
    end
    -- ...
    return BaseWidget

    -- my_widget.lua
    local BaseWidget = require("ui/widget/base_widget")
    local MyWidget = BaseWidget:extend({
        -- MyWidget's properties and methods
    })
    ```
*   **Instantiation:** The `:new()` method is used to create instances. It typically calls an `:init()` method for instance-specific setup.
    ```lua
    function MyWidget:new(options)
        local o = self:extend(options)
        o:init()
        return o
    end

    function MyWidget:init()
        self.my_property = "default_value"
    end
    ```
*   **Example:** Refer to `frontend/ui/widget/widget.lua` for the canonical implementation of the base widget "class".

### 3.3. Event Handling

The application uses a centralized event dispatching system.

1.  **Events** are defined as tables, typically in `frontend/ui/event.lua`.
2.  **Widgets** that need to listen to events inherit from `EventListener`.
3.  **`UIManager`** is the central event bus. Widgets register with it to receive events.
4.  **`Dispatcher`** (`frontend/dispatcher.lua`) is used to trigger high-level user actions (e.g., from menus or gestures). It creates `Event` objects and sends them to `UIManager`.

**To add a new user-facing action, you will likely need to modify `frontend/dispatcher.lua`.**

## 4. Testing Guidelines

Adding tests for new features and bug fixes is highly encouraged.

*   **Location:** Tests are located in `spec/`. Unit tests are in `spec/unit/`.
*   **Naming:** Test files must have a `_spec.lua` suffix (e.g., `my_module_spec.lua`).
*   **Framework:** The project uses a BDD-style testing framework similar to Busted. Use `describe` to group tests and `it` for individual test cases.

```lua
-- spec/unit/my_module_spec.lua
describe("MyModule", function()
    local MyModule

    -- Use setup to require modules and prepare the test environment
    setup(function()
        require("commonrequire") -- This is often needed
        MyModule = require("path/to/my_module")
    end)

    it("should do something correctly", function()
        -- 1. Create an instance of the object to test
        local my_instance = MyModule:new()
        -- 2. Perform an action
        local result = my_instance:some_function()
        -- 3. Assert the outcome
        assert.is.truthy(result)
        assert.are.equal(result, "expected_value")
    end)

    it("should handle errors", function()
        assert.has_error(function()
            MyModule:function_that_should_fail()
        end)
    end)
end)
```
*   **Running Tests:** Use the `./kodev` script.
    *   `./kodev test front`: Run all frontend tests.
    *   `./kodev test front my_module_spec.lua`: Run a specific test file.

## 5. Code Style & Linting

The project uses `luacheck` to enforce code style.

*   **Configuration:** The rules are defined in `.luacheckrc`.
*   **Running the linter:**
    ```bash
    make static-check
    ```
    **You must run this command and fix all warnings before submitting your changes.**

### Key Style Rules:

*   **Globals:** Do not introduce new global variables. All variables should be `local`.
*   **Unused Variables:** If a local variable is intentionally unused (e.g., a function argument you don't need), prefix it with an underscore: `local _unused_var`.
*   **Line Length:** While not strictly enforced by the linter, keep lines to a reasonable length (e.g., around 100 characters) for readability.
