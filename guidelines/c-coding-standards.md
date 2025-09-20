---
layout: default
title: C Coding Standards
parent: Guidelines
nav_order: 1
---

# C Coding Standards

This document outlines the C coding standards and best practices to be followed in all C development projects.

## Important Note

**Rules are there to be broken.** Two good reasons to break a particular rule:

1. **When applying the rule would make the code less readable**, even for someone who is used to reading code that follows the rules.
2. **To be consistent with surrounding code that also breaks it** (maybe for historic reasons) – although this is also an opportunity to clean up someone else's mess (in true XP style).

## Standards Foundation

Our C coding standards are based on:

- **Michael Barr Embedded C Coding Standard (2018 Edition)** - [MB book](https://barrgroup.com/sites/default/files/barr_c_coding_standard_2018.pdf)
- **NASA Power of Ten Rules** - [Wikipedia](https://en.wikipedia.org/wiki/The_Power_of_10:_Rules_for_Developing_Safety-Critical_Code)
- **Selected MISRA-C Guidelines**

---

## Code Style and Formatting

### Line Length and Function Size

- **Maximum line length**: 100 characters
- **Maximum function length**: 100 lines
- Break long lines at logical points (after operators, commas, etc.)

```c
/* Good: Line broken at logical point with Yoda conditions */
if ((EXPECTED_VALUE == condition_one) && 
    (INVALID_STATE != condition_two) &&
    (MINIMUM_THRESHOLD < condition_three)) {
  perform_action();
}

/* Bad: Line too long and no Yoda conditions */
if ((condition_one == EXPECTED_VALUE) && (condition_two != INVALID_STATE) && (condition_three > MINIMUM_THRESHOLD))
```

### Indentation and Braces

- Use **2 spaces** for indentation (no tabs)
- Opening braces on **same line** for control structures (`if`, `while`, `for`, etc.)
- Opening braces on **new line** for function definitions only
- Closing braces aligned with opening statement

### Code Structure

- One space between keywords (`if`, `for`, `while`, etc.) and the following left parenthesis
- No spaces inside the parentheses
- **Braces are required everywhere**, even where C permits them to be omitted
- Do not add braces to code you are not otherwise modifying
- All new C code requires braces
- Braces should be formatted as shown:

```c
/* Correct brace style with Yoda conditions */
if (NULL != var) {
  ...
}
else {
  ...
}

/* Also correct for single statements - braces required */
if (ERROR_CODE == result) {
  return -1;
}

/* Incorrect - missing braces */
if (ERROR_CODE == result)
  return -1;  /* BAD: No braces */
```

```c
/* Function braces on new line, control structures on same line */
int calculate_checksum(uint8_t *data, size_t length)
{
  uint16_t checksum = 0;
  
  if (NULL == data) {
    return -1;
  }
  
  for (size_t idx = 0; idx < length; idx++) {
    checksum += data[idx];
  }
  
  return checksum;
}
```

---

## Yoda Conditions

**Always use Yoda conditions** for comparisons to prevent accidental assignment.

```c
/* Good: Yoda conditions */
if (NULL == pointer)
if (0 == counter)
if (MAX_SIZE <= buffer_length)
if (ERROR_CODE != result)

/* Bad: Regular conditions (risk of assignment) */
if (pointer == NULL)  /* Could accidentally write = */
if (counter == 0)     /* Could accidentally write = */
```

### Yoda Condition Examples

```c
/* Constant comparisons */
if (42 == magic_number)
if (READY == system_state)
if (0xFF == register_value)

/* Range checks */
if ((MIN_VALUE <= input) && (MAX_VALUE >= input))

/* Null pointer checks */
if (NULL != buffer) {
  process_buffer(buffer);
}
```

---

## Michael Barr Embedded C Coding Standard (2018)

### Naming Conventions

#### Variables and Functions

- Use **snake_case** for variables and functions
- Variable names must contain **3 or more characters**
- Variables **cannot start with underscore**
- Use descriptive names (avoid abbreviations)
- Prefix global variables with `g_`
- Prefix static variables with `s_`

```c
/* Good naming */
uint16_t sensor_reading;
static uint8_t s_error_count;
uint8_t g_system_mode;

void initialize_uart_module(void);
bool is_data_valid(uint8_t *data, size_t length);

/* Bad naming */
uint16_t sr;              /* Too short - less than 3 chars */
uint8_t ec;               /* Too short - less than 3 chars */
void init(void);          /* Abbreviation - not descriptive */
uint8_t _count;           /* Starts with underscore */
int i, j;                 /* Single character variables */
```

#### Constants and Macros

- Use **SCREAMING_SNAKE_CASE** for constants and macros
- Wrap multi-token macros in parentheses

```c
#define MAX_BUFFER_SIZE     (256U)
#define TIMEOUT_MS          (1000U)
#define PI_APPROXIMATION    (3.14159f)

#define CALCULATE_CRC(data, len) \
    calculate_checksum((data), (len))
```

### Data Types

- Use fixed-width integer types from `<stdint.h>`
- Use `bool` type from `<stdbool.h>`
- Explicitly specify signed/unsigned

```c
#include <stdint.h>
#include <stdbool.h>

uint8_t  byte_value;    /* 8-bit unsigned */
int16_t  signed_word;   /* 16-bit signed */
uint32_t address;       /* 32-bit unsigned */
bool     is_enabled;    /* Boolean */
```

---

## NASA Power of Ten Rules

### 1. Avoid Complex Flow Constructs

- No `goto` statements
- No `setjmp` or `longjmp`
- Minimize use of recursion

```c
/* Good: Simple control flow */
for (int idx = 0; idx < MAX_ITERATIONS; idx++) {
  if (ERROR_CONDITION == process_item(idx)) {
    break;
  }
}

/* Avoid: goto statements */
```

### 2. Give All Loops a Fixed Upper Bound

- Every loop must have a deterministic termination condition
- Use loop counters to prevent infinite loops

```c
/* Good: Bounded loop */
uint8_t timeout_counter = 0;
while ((READY != system_status()) && (MAX_WAIT_CYCLES > timeout_counter)) {
  timeout_counter++;
  delay_ms(10);
}

/* Bad: Potentially infinite loop */
while (READY != system_status()) {
  delay_ms(10);  /* Could wait forever */
}
```

### 3. No Dynamic Memory Allocation

- Avoid `malloc()`, `calloc()`, `realloc()`, `free()`
- Use static memory allocation
- Pre-allocate all required memory

```c
/* Good: Static allocation */
#define MAX_ENTRIES (100U)
static sensor_data_t s_sensor_buffer[MAX_ENTRIES];

/* Avoid: Dynamic allocation */
/* sensor_data_t *buffer = malloc(size * sizeof(sensor_data_t)); */
```

### 4. Restrict Function Size

- Functions should not exceed **100 lines**
- Keep functions focused on single responsibility

### 5. Limit Variable Scope

- Declare variables in smallest possible scope
- Initialize variables at declaration

```c
/* Good: Limited scope */
void process_sensors(void)
{
  for (uint8_t idx = 0; idx < SENSOR_COUNT; idx++) {
    uint16_t reading = read_sensor(idx);  /* Declared where used */
    if ((VALID_RANGE_MIN <= reading) && (VALID_RANGE_MAX >= reading)) {
      store_reading(idx, reading);
    }
  }
}
```

---

## Key MISRA-C Guidelines

### Essential Type Safety

#### Rule 9.1: Variable Initialization

- **All variables must be initialized before use**
- Initialize at declaration whenever possible
- Avoid uninitialized variables that could lead to undefined behavior

```c
/* Good: Always initialize variables */
uint8_t counter = 0;
bool flag_status = false;
uint16_t sensor_value = 0xFFFF;  /* Default invalid value */
char buffer[BUFFER_SIZE] = {0};  /* Initialize array to zeros */

/* Bad: Uninitialized variables */
uint8_t counter;
bool flag_status;
counter++;  /* DANGEROUS: Using uninitialized variable */

/* Good: Initialize pointers */
uint8_t *ptr = NULL;
if (NULL != ptr) {
  *ptr = 0x55;
}

/* Bad: Uninitialized pointer */
uint8_t *ptr;  /* Could point anywhere */
*ptr = 0x55;   /* DANGEROUS: Undefined behavior */
```

#### Rule 10.1: No Implicit Conversions

```c
/* Good: Explicit casting */
uint16_t large_value = 1000U;
uint8_t small_value = (uint8_t)(large_value & 0xFFU);

/* Bad: Implicit conversion */
/* uint8_t small_value = large_value; */
```

#### Rule 10.3: Assignment Operator Type Safety

```c
/* Good: Compatible types */
uint32_t count = 0U;
uint32_t increment = 1U;
count += increment;

/* Bad: Mixed signedness */
/* int32_t signed_val = -1;
   uint32_t unsigned_val = signed_val; */
```

### Pointer Safety

#### Rule 18.1: Pointer Arithmetic Restrictions

```c
/* Good: Array indexing */
uint8_t buffer[BUFFER_SIZE];
for (uint8_t idx = 0; idx < BUFFER_SIZE; idx++) {
  buffer[idx] = read_byte();
}

/* Avoid: Pointer arithmetic */
/* uint8_t *ptr = buffer;
   *(ptr + idx) = read_byte(); */
```

#### Rule 18.6: Address of Automatic Variables

```c
/* Good: Static or global scope */
static uint8_t s_persistent_buffer[SIZE];
uint8_t* get_buffer_pointer(void)
{
  return s_persistent_buffer;
}

/* Bad: Address of local variable */
/* uint8_t* get_buffer_pointer(void)
{
  uint8_t local_buffer[SIZE];
  return local_buffer;  // Undefined behavior
} */
```

### Control Flow

#### Rule 15.5: Single Exit Point

```c
/* Good: Single return */
int validate_input(uint8_t input)
{
  int result = ERROR_INVALID;
  
  if ((MIN_VALUE <= input) && (MAX_VALUE >= input)) {
    if (true == is_input_allowed(input)) {
      result = SUCCESS;
    }
  }
  
  return result;
}
```

#### Rule 16.4: Switch Statement Default

```c
/* Good: Always include default */
switch (system_state) {
  case STATE_INIT:
    initialize_system();
    break;
    
  case STATE_RUNNING:
    process_main_loop();
    break;
    
  case STATE_ERROR:
    handle_error_condition();
    break;
    
  default:
    /* Should never reach here */
    trigger_fault_handler();
    break;
}
```

---

## Error Handling

### Return Value Conventions

- Use return codes for error conditions
- Reserve special values for errors
- Document return value meanings

```c
typedef enum {
  RESULT_SUCCESS = 0,
  RESULT_ERROR_NULL_POINTER = -1,
  RESULT_ERROR_INVALID_PARAMETER = -2,
  RESULT_ERROR_TIMEOUT = -3
} result_code_t;

result_code_t initialize_module(const config_t *config)
{
  if (NULL == config) {
    return RESULT_ERROR_NULL_POINTER;
  }
  
  if (false == is_config_valid(config)) {
    return RESULT_ERROR_INVALID_PARAMETER;
  }
  
  /* Initialization logic here */
  
  return RESULT_SUCCESS;
}
```

### Assertion

```c
#include <assert.h>

void process_data(uint8_t *buffer, size_t length)
{
  assert(NULL != buffer);           /* Debug check */
  assert(0 < length);              /* Debug check */
  assert(MAX_LENGTH >= length);    /* Debug check */
  
  /* Runtime checks for production */
  if (NULL == buffer) {
    log_error("Null buffer passed to process_data");
    return;
  }
  
  /* Process data */
}
```

---

## Documentation Requirements

### Function Documentation (Doxygen)

```c
/**
 * @brief Calculate CRC16 checksum for given data buffer
 * 
 * @param[in] data      Pointer to data buffer (must not be NULL)
 * @param[in] length    Length of data buffer in bytes (must be > 0)
 * @param[in] initial   Initial CRC value (typically 0x0000 or 0xFFFF)
 * 
 * @return CRC16 checksum value
 * @retval 0x0000-0xFFFF Calculated CRC value
 * 
 * @note This function implements the CRC-16-CCITT polynomial
 * @warning Passing NULL pointer results in undefined behavior
 * 
 * @code
 * uint8_t data[] = {0x01, 0x02, 0x03};
 * uint16_t crc = calculate_crc16(data, sizeof(data), 0xFFFF);
 * @endcode
 */
uint16_t calculate_crc16(const uint8_t *data, size_t length, uint16_t initial);
```

### File Headers (Doxygen)

```c
/**
 * @file    sensor_manager.c
 * @brief   Sensor data acquisition and processing module
 * @author  Development Team
 * @date    2024-XX-XX
 * @version 1.0.0
 * 
 * @details This module handles initialization, reading, and processing of
 *          sensor data from multiple sensor types.
 * 
 * @copyright Copyright (c) 2024
 */
```

---

## Summary Checklist

- [ ] Functions ≤ 100 lines
- [ ] Lines ≤ 100 characters  
- [ ] Use Yoda conditions for all comparisons and logical operations
- [ ] Use 2 spaces for indentation
- [ ] Variable names ≥ 3 characters, no underscore prefix
- [ ] All variables initialized before use
- [ ] Use fixed-width integer types (`uint8_t`, `int16_t`, etc.)
- [ ] No dynamic memory allocation
- [ ] All loops have fixed upper bounds
- [ ] Single exit point per function
- [ ] Variables declared in minimal scope
- [ ] Explicit type casting when needed
- [ ] Comprehensive error handling
- [ ] Complete Doxygen documentation
