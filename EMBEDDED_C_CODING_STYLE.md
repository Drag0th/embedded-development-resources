# Embedded C Coding Style

**Version:** 1.0  
**Language:** C

---

## 1. Purpose and Principles

This document defines a consistent coding style for embedded C projects. It establishes conventions for formatting, naming, source-code organization, and implementation structure.

The following principles guide this coding style:

- Follow the existing style of the codebase when modifying existing code.
- Do not introduce multiple naming or formatting conventions into the same project.
- Prefer standard C language features over compiler-specific extensions.
- Avoid unnecessary abstractions and premature optimization.
- Do not sacrifice readability for fewer lines of code.
- Prefer well-defined behavior over relying on compiler-specific behavior.
- Keep functions, modules, and files focused on a single responsibility.

**This document is a personal coding style guide intended to maintain consistency across projects. It is not an official standard or a claim of compliance with any referenced standard or guideline.**

## 2. Formatting

### 2.1 Indentation and Braces

Use 4 spaces for indentation; do not use tabs.

Place opening braces on a new line (Allman style). Apply this convention consistently to functions, conditional statements, loops, structures, enumerations, and unions.

Always use braces for conditional and loop bodies, even when they contain a single statement.

```c
void performAction(void)
{
    if (condition)
    {
        executeAction();
    }
    else
    {
        handleError();
    }
}
```

### 2.2 Spacing

- Use spaces around operators.
- Use a space after commas.
- Do not insert unnecessary spaces inside parentheses or before punctuation.
- Keep pointer declaration formatting consistent.

```c
int result = firstValue + secondValue;

if ((value > minimum) && (value < maximum))
{
    processValue(value, minimum, maximum);
}

uint8_t *buffer;
```

### 2.3 Line Length and Blank Lines

- Keep lines under 100 characters where practical.
- Split long expressions, declarations, and argument lists when doing so improves readability.
- Use blank lines to separate logical sections and distinct operations.
- Avoid excessive blank lines.
- Group related declarations together and separate them from executable statements when this improves readability.

```c
void processData(void)
{
    int inputValue = 10;
    int outputValue = 0;

    outputValue = calculateResult(inputValue);

    storeResult(outputValue);
}
```

## 3. Naming Conventions

Use the following conventions consistently throughout a project.

| Identifier | Convention | Example |
|---|---|---|
| Structure types | `PascalCase_t` | `DeviceConfig_t` |
| Enumeration types | `snake_case_t` | `operating_mode_t` |
| Union types | `PascalCase_t` | `DataPacket_t` |
| Module functions | `Module_Operation` | `UART_Write` |
| Function parameters | `camelCase` | `retryCount` |
| Local variables | `camelCase` | `inputValue` |
| Global variables | `g_PascalCase` | `g_UartBytesLeft` |
| File-local static variables | `s_PascalCase` | `s_CurrentState` |
| File-scope constant objects | `kPascalCase` | `kDefaultTimeout` |
| Structure and union members | `camelCase` | `deviceStatus` |
| Enumerators | `ePascalCase` | `eIdle` |
| Module-specific enumerators | `eModule_PascalCase` | `eUart_Timeout` |
| Macros | `UPPER_SNAKE_CASE` | `MAX_BUFFER_SIZE` |
| Files | `lowercase_snake_case` | `data_processor.c` |

### 3.1 Identifier Guidelines

- Choose names that describe the purpose of the identifier rather than its type.
- Function names should describe an action and begin with a verb or verb phrase.
- Boolean names should communicate a condition or state. Prefer prefixes such as `is`, `has`, `can`, or `should`.
- Short names are acceptable in small, clearly defined scopes, such as loop counters.
- Avoid vague names such as `data`, `value`, `temp`, or `flag` when a more descriptive name is practical.
- Use the same spelling and capitalization for an identifier everywhere it is referenced.

```c
bool isInitialized;
bool hasError;
bool canTransmit;

for (size_t itemIndex = 0U; itemIndex < itemCount; itemIndex++)
{
    processItem(itemIndex);
}
```

### 3.2 File Naming

Use descriptive, lowercase `snake_case` file names.

Prefer:

- `data_processor.c`
- `data_processor.h`
- `configuration_manager.c`
- `configuration_manager.h`

Use matching base names for a module's source and header files. Avoid inconsistent capitalization, vague numbering, or temporary suffixes, such as `DataProcessor.c`, `module1.c`, or `DataProcessorFinal.c`.

### 3.3 Constants and Macros

Use `UPPER_SNAKE_CASE` for macros.

Use the `kPascalCase` convention for file-scope constant objects. Use `const` when an object should not be modified through the identifier used to access it.

For integer constants that must be usable in preprocessor expressions, use macros. For named integral values that do not need preprocessor evaluation, an enumeration may be appropriate.

Replace unexplained numeric literals with named constants when doing so makes their meaning clearer. Simple, self-explanatory values such as `0` and `1` may remain literals where their meaning is clear.

```c
#define MAX_RETRY_COUNT 3U

static const uint16_t kDefaultTimeoutMs = 1000U;

if (retryCount > MAX_RETRY_COUNT)
{
    handleFailure();
}
```

## 4. Source File Organization

### 4.1 File and Module Responsibilities

- Give each source file a clearly defined responsibility.
- Keep unrelated functionality in separate files.
- Avoid excessively large source files.
- Keep public interfaces separate from implementation details.
- Avoid unnecessary dependencies between modules.
- Do not expose internal implementation details through public headers.

Use `static` for file-scope functions and variables that should be private to their source file.

```c
static uint16_t s_CurrentSpeed;

static void updateMotorState(void)
{
    /* Internal implementation */
}
```

### 4.2 Header Files

Header files (`.h`) should expose declarations needed by other source files while keeping implementation details in source files (`.c`).

- Each header should include the dependencies required by its declarations.
- Use include guards in every header file.
- Avoid placing unnecessary implementation details in headers.
- Keep public declarations organized and minimal.
- Include the corresponding module header first in its source file.

```c
#ifndef DATA_PROCESSOR_H
#define DATA_PROCESSOR_H

#include <stdbool.h>
#include <stddef.h>
#include <stdint.h>

void DataProcessor_Initialize(void);
bool DataProcessor_Process(void);

#endif /* DATA_PROCESSOR_H */
```

### 4.3 Include Order

Group includes and separate the groups with a blank line:

1. Corresponding module header.
2. C standard library headers.
3. External library headers.
4. Project headers.

```c
#include "data_processor.h"

#include <stdbool.h>
#include <stddef.h>
#include <stdint.h>

#include <external_library.h>

#include "configuration.h"
#include "system_state.h"
```

## 5. Variables, Types, and Constants

### 5.1 Initialization and Const Qualification

Initialize variables when they are declared whenever practical. Avoid leaving local variables uninitialized unless they are assigned before their first read.

Use `const` for objects that should not be modified through the relevant identifier. For pointer declarations, place `const` deliberately to express whether the pointed-to data, the pointer itself, or both must remain unchanged.

```c
uint16_t sampleCount = 0U;
const uint16_t kMaximumSamples = 128U;

const uint8_t *inputBuffer = NULL;
uint8_t * const outputBuffer = NULL;
```

### 5.2 Integer Types

Use fixed-width integer types from `<stdint.h>` when the exact bit width is important. Do not assume that fundamental integer types such as `int` or `long` have a particular width.

```c
#include <stdint.h>

uint8_t status;
uint16_t counter;
uint32_t timestamp;
int32_t result;
```

Use `size_t` for object sizes, array sizes, and indexing where appropriate.

Use unsigned suffixes (`U`) for unsigned integer constants. Use a suffix such as `UL` only when the constant's required type calls for it.

```c
uint32_t retryCount = 3U;
size_t bufferLength = 128U;
```

### 5.3 Floating-Point Values

Use `float` when single precision is sufficient.

Avoid exact equality comparisons for floating-point values unless the application explicitly requires them. Use an application-appropriate tolerance when comparing approximate values.

```c
if (fabsf(valueA - valueB) < tolerance)
{
    processEqualValues();
}
```

### 5.4 Type Aliases

Use `typedef` when it improves readability or simplifies complex declarations.

Prefer descriptive type names and maintain the naming conventions defined in Section 3.

```c
typedef struct
{
    uint32_t identifier;
    uint16_t length;
    uint8_t status;
} PacketHeader_t;
```

Avoid aliases that obscure the underlying type without providing a meaningful abstraction.

## 6. Functions

### 6.1 Responsibility and Length

Each function should perform one clearly defined task. Keep functions focused and reasonably short.

- Avoid combining unrelated operations.
- Avoid deeply nested logic; use early returns when they make the control flow clearer.
- Extract repeated or logically independent operations into helper functions.
- Do not split simple code into unnecessarily small functions.

### 6.2 Function Declarations and Parameters

Use descriptive parameter names.

For input parameters that do not need to be modified, use `const` where appropriate.

Use pointers for output parameters and make their purpose clear. Use the return value to communicate the operation's status or primary result when appropriate.

```c
bool calculateAverage(
    const int *values,
    size_t valueCount,
    float *result
);
```

When a function requires many related parameters, consider grouping them into a structure. Do not introduce a structure solely to avoid a small number of straightforward parameters.

### 6.3 Return Values

Use an appropriate return type to communicate the result or status of a function.

Use `void` when a function has no result or status that the caller needs to receive.

```c
void UART_Initialize(void);

bool UART_Transmit(const uint8_t *data, size_t length);

uint16_t ADC_ReadValue(void);
```

Avoid using global variables to communicate ordinary function results when a return value or output parameter provides a clearer interface.

## 7. Control Flow and Expressions

### 7.1 Conditional Statements

Use braces for all conditional blocks, including single-statement blocks.

Prefer early returns when they reduce unnecessary nesting and make the main execution path easier to follow.

```c
bool processData(const Data_t *data)
{
    if (data == NULL)
    {
        return false;
    }

    if (!isValid(data))
    {
        return false;
    }

    performProcessing(data);

    return true;
}
```

### 7.2 Switch Statements

Use `switch` when selecting between multiple discrete values.

- Include `break` where fall-through is not intended.
- Handle unexpected values with `default` where appropriate.
- Document intentional fall-through explicitly.
- Do not rely on accidental fall-through.
- Use braces around case bodies consistently.

```c
switch (operatingMode)
{
    case eIdle:
    {
        enterIdleMode();
        break;
    }

    case eActive:
    {
        enterActiveMode();
        break;
    }

    case eError:
    {
        handleError();
        break;
    }

    default:
    {
        handleUnknownMode();
        break;
    }
}
```

### 7.3 Loops

Use the loop type that best represents the intended behavior.

Keep termination conditions clear and avoid modifying loop counters in unexpected places.

```c
for (size_t itemIndex = 0U; itemIndex < itemCount; itemIndex++)
{
    processItem(itemIndex);
}

while (isActive)
{
    updateState();
}
```

### 7.4 Expressions and Operators

- Use parentheses when they improve clarity; do not rely on readers remembering complicated operator precedence.
- Split complicated expressions into intermediate variables when the intermediate steps have meaningful names or improve readability.
- Avoid modifying the same variable multiple times in one expression.
- Use increment and decrement operators consistently.

```c
int adjustedValue = inputValue - offset;
int result = adjustedValue * multiplier;

retryCount++;
```

Avoid expressions with multiple side effects, such as:

```c
value = counter++ + ++counter;
```

## 8. Pointers and Memory Management

### 8.1 Pointer Declaration and Initialization

Initialize pointers when they are declared whenever practical.

Use `NULL` to represent a null pointer. Keep pointer declaration formatting consistent.

```c
int *valuePtr = NULL;
const uint8_t *inputBuffer = NULL;
uint8_t *outputBuffer = NULL;
```

### 8.2 Pointer Usage

- Prefer array indexing over pointer arithmetic when it improves readability.
- Keep pointer arithmetic within the bounds of the relevant object or array.
- Use `const` for data that should not be modified through a pointer.
- Avoid unnecessary pointer casts.
- Do not use pointers when a simpler value-based interface is sufficient.

### 8.3 Dynamic Memory Allocation

In embedded C, prefer static allocation over dynamic allocation whenever practical.

- Avoid dynamic memory allocation unless it is justified by the application.
- Avoid allocating or freeing memory repeatedly during normal program operation.
- Keep allocation and deallocation responsibilities clear.
- Avoid unnecessary dynamic memory management abstractions.

## 9. Structures, Enumerations, and Unions

### 9.1 Structures

Use structures to group logically related data.

- Use descriptive member names.
- Keep structures focused on a single concept.
- Initialize members when practical.
- Avoid unnecessary nesting.
- Do not rely on structure padding or memory layout unless explicitly required.

```c
typedef struct
{
    uint32_t identifier;
    uint16_t length;
    uint8_t status;
} PacketHeader_t;
```

### 9.2 Enumerations

Use enumerations instead of unexplained integer values when representing a set of named states or options.

Use the established enumeration naming convention consistently.

```c
typedef enum
{
    eIdle,
    eActive,
    eError
} operating_mode_t;
```

Use explicit enumerator values when the numeric representation is part of an interface or has a defined meaning.

```c
typedef enum
{
    eStatus_Ok = 0U,
    eStatus_Error = 1U,
    eStatus_Timeout = 2U
} device_status_t;
```

### 9.3 Unions

Use unions only when there is a clear requirement for shared storage.

Keep the intended interpretation of union members clear and document the representation when necessary. Avoid using unions as a shortcut for type conversion.

## 10. Preprocessor and Macros

Use macros sparingly. Prefer language features such as `const`, enumerations, and `static` functions when they provide a clearer solution.

Macros do not follow normal language scope and type rules.

### 10.1 Macro Definitions

Use uppercase `snake_case` for macro names.

Avoid generic names that may conflict with other identifiers. Prefix project- or module-specific macros when necessary to reduce naming conflicts.

```c
#define DATA_PROCESSOR_MAX_BUFFER_SIZE 256U
#define DATA_PROCESSOR_DEFAULT_TIMEOUT_MS 1000U
```

### 10.2 Function-Like Macros

Parenthesize macro parameters and the entire expression where appropriate.

Be aware that macro arguments may be evaluated more than once. Prefer functions when possible.

```c
#define SQUARE(value) ((value) * (value))
```

### 10.3 Conditional Compilation

Keep conditional compilation simple and localized.

Avoid complex nested preprocessor conditions when ordinary C language constructs are sufficient.

```c
#ifdef FEATURE_ENABLED

static void performFeature(void)
{
    /* Implementation */
}

#endif /* FEATURE_ENABLED */
```

## 11. Comments and Documentation

### 11.1 General Rules

Comments should explain why something is done, not simply repeat what the code already says.

Avoid unnecessary, outdated, or misleading comments.

```c
/* Skip the reserved identifier. */
counter++;
```

### 11.2 Comment Style

Use `//` for short comments and `/* ... */` for longer descriptions when appropriate and supported by the project's C language version.

Use `/* ... */` when compatibility with older C standards is required. Be consistent within the project.

### 11.3 Function Documentation

Document public functions when their purpose, parameters, return values, or limitations are not obvious.

Use a consistent documentation format throughout the project.

```c
/**
 * @brief Calculates the average of an array.
 *
 * @param values Pointer to the input array.
 * @param valueCount Number of elements in the array.
 * @param result Pointer to the output value.
 *
 * @return true if the calculation succeeds, false otherwise.
 */
bool calculateAverage(
    const float *values,
    size_t valueCount,
    float *result
);
```

## 12. Arrays and Strings

### 12.1 Array Declarations and Size

Use descriptive names for arrays.

Avoid duplicating array-size literals unnecessarily. When possible, calculate the number of elements from the array declaration.

```c
#define ARRAY_SIZE(array) (sizeof(array) / sizeof((array)[0]))

uint8_t receiveBuffer[128];

size_t receiveBufferSize = ARRAY_SIZE(receiveBuffer);
```

The `ARRAY_SIZE` macro is suitable only for actual arrays, not pointers.

### 12.2 C Strings

Remember that C strings require a terminating null character.

- Use bounded operations and verify buffer capacity.
- Prefer functions with explicit size limits when appropriate.
- Keep string buffer sizes clear and consistent.
- Account for the terminating null character when defining buffer capacities.

---

**End of document.**
