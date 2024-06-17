## Introduction

* Communication Access Programming Language (CAPL) allows programming of network node models as well as special evaluation programs for individual applications.
* The functional range of CANoe includes a CAPL compiler which compiles a created CAPL file with the extension *.CAN to an executable program file with the extension *.CBF.
* CAPL programs can be used to analyze/simulate network data.
* CAPL program can be used to simulate the rest of the network.
* CAPL program can be used to automate test cases.

## Prerequisites for CAPL programming

* Knowledge of Vector CANoe tool.
* Basic knowledge of the C programming language.
* Knowledge of database and bus protocols(minimum CAN).
* Windows PC with minimum 8GB RAM.
* Vector CANoe version (>= 11.0) installed. At Least demo version.
* Download latest Vector CANoe demo version from here → [Download | Vector](https://www.vector.com/int/en/download/canoe-demo-17-windows/)

## **Program Block**

|           | SIMULATION                                                   | ANALYSIS                                                 |
| --------- | ------------------------------------------------------------ | -------------------------------------------------------- |
| CANoe     | ![canoe_simulation](./canoe_capl_demo_cfg/doc_images/canoe_simulation.png)         | ![canoe_analysis](./canoe_capl_demo_cfg/doc_images/canoe_analysis.png)           |
| CANalyzer | ![canalyzer_simulation](./canoe_capl_demo_cfg/doc_images/canalyzer_simulation.png) | ![canalyzer_analysis](./canoe_capl_demo_cfg/doc_images/canalyzer_analysis.png) |

## Programming Environment

| CANoe     | ![canoe_capl_programming_env](./canoe_capl_demo_cfg/doc_images/canoe_capl_programming_env.png)         |
| --------- | -------------------------------------------------------------------------------- |
| CANalyzer | ![canalyzer_capl_programming_env](./canoe_capl_demo_cfg/doc_images/canalyzer_capl_programming_env.png) |

## Variables

### Scalar Data Types

#### Integers

```c
// byte (unsigned, 1 Byte)
byte day;

// word (unsigned, 2 Byte)
word month;

// dword (unsigned, 4 Byte)
dword year;

// int (signed, 2 Byte)
int angle;

// long (signed, 4 Byte)
long profit_in_bucks;

// int64 (signed, 8 Byte)
int64 ticks;

// qword (unsigned, 8 Byte)
qword timestamp;
```

#### Individual character

```c
// char (1 Byte)
char key_pressed;
```

#### Floating point numbers

```c
// float (8 Byte)
float pi_value = 3.14;

// double (8 Byte)
double stock_value = 5.123456;
```

### Self Defined Structures(struct)

```c
variables
{
    struct PairStructType { int first; int second } pair;
    struct PairStructType pair2;
}
on start
{
    pair.first = 1;
    pair.second = 2;
}
```

### Enumeration Types(enum)

Enumeration types are defined in CAPL in exactly the same way as in C:
enum Colors { Red, Green, Blue };
Element names must be unique throughout the CAPL program.

### Associative Fields

With associative fields (so-called maps) you can perform a 1:1 assignment of values to other values without using excessive memory. The elements of an associative field are key value pairs, whereby there is fast access to a value via a key.
An associative field is declared in a similar way to a normal field but the data type of the key is written in square brackets:

```c
int m[float];         // maps float to int
float x[int64];       // maps int64 to float
char[30] s[ char[] ]  // maps string (of unspecified length) to string of length < 30
```

Data types for the keys can be long, int64, float, double, enumeration types and char[]. As data type for values are simple data types, enumeration types fields and structure types allowed. You cannot use associative fields themselves as the value type of an associative field.

### Objects

* message and multiplexed_message
* signal
* sysVar, sysVarInt, sysVarFloat, sysVarString, sysVarIntArray, sysVarFloatArray, sysVarData
* timer and msTimer
* diagRequest
* diagResponse

### Global Variables

* global variables are declared in the Variables section.
* The data types DWORD, LONG, WORD, INT, BYTE and CHAR can be used analogously to their use in the C programming language.
* The data types FLOAT and DOUBLE are synonyms and designate 64 bit floating point numbers conforming to the IEEE standard.
* A timer is created with a timer. The timer does not begin to run until it has been started in a on timer event procedure. After the timer has elapsed the associated event procedure is called. A variable of the type timer can only be accessed by the predefined functions setTimer and cancelTimer.
* CAN messages to be output by the CAPL program are declared with a message.
* Variables can be initialized in their declarations. Both simplified notation and bracketing with { } are permitted. The compiler initializes all variables, with the exception of timers, with default values (automatic default: 0).

### Local Variables

* Local variables are always created statically in CAPL (in contrast to C).
* This means that an initialization is only executed at the program start, and when variables enter the procedure they assume the value they had when they last left the procedure.

```c
variables
{
    int j, k = 2;
    double f = 17.5;
    msTimer tmr;
    message 100 msg;
    int array_var[2] = [1, 2];
    char name[12] = "hello world";
    int array_matrix[2][2] = {{1, 2}, {3, 4}};
}
```

## System Events

### on preStart

* Initialization of measurement (before start)
* The on preStart procedure is only used to initialize variables, to display messages in the Write Window and to read in data from files.
* At the moment the on preStart procedure is executed, not all possibilities of the system (CANoe) are available.
* It is not possible for example to send messages on the bus with the output function.

```c
on preStart
{
    write("hello from prestart");
}
```

### on start

* Program start

```c
on start
{
    write("hello from start");
}
```

### on preStop

* Measurement stop has been requested
* The on preStop handler is called after a measurement stop has been requested.
* The on preStop function can be used to carry out some final actions that must be done before the measurement stop actually takes effect.

```c
on preStop
{
    write("hello from preStop");
}
```

### on stopMeasurement

* End of measurement

```c
on stopMeasurement
{
    write("hello from stopMeasurement");
}
```

### on timer

* You can define time events in CAPL. When this event occurs, i.e. when a certain period of time elapses, the associated on timer procedure is called. You can program cyclic program sequences by resetting the same time event within the on timer procedure.
* The timer variable can be accessed with the keyword this within the event procedure.
* You would start a previously-defined timer with the function setTimer.
* After the timer has elapsed, the associated on timer procedure is called. The maximum time is 2147483647 s (=596523.23h) for variables of the type timer and 2147483647 ms (= 2147483,647 s = 596,52h) for variables of the type msTimer. With the function cancelTimer you can stop a timer which has already been started and thereby prevent the associated on timer procedure from being called.
* In CAPL has the following type of timer's:
  * timer - timer based on seconds
  * msTimer - timer based on milliseconds

```c
variables
{
    msTimer tmr;
    message 100 msg;
}

on key 'a'
{
    setTimer(tmr, 1000);
}

on timer tmr
{
    output(msg);
}
```

### on key

* With on key procedure you can execute defined actions with a key press.

| Keystroke          | Event Procedure     | Occurs When                                            |
| ------------------ | ------------------- | ------------------------------------------------------ |
| a                  | on key 'a'          | lower case "a" key is pressed                          |
| A                  | on key 'A'          | upper case "A" key is pressed                          |
| A                  | on key 0x41         | upper case "A" key is pressed                          |
| 2                  | on key '2'          | number "2" is pressed                                  |
| $                  | on key '\\$'        | "\$" key is pressed                                    |
| End                | on key End          | key "End" is pressed                                   |
| Control + PageDown | on key ctrlPageDown | key "Control + Page Down" keys pressed                 |
| any key            | on key *            | any key pressed other than already defined key events |

```c
on key 'a'
{
    message 100 msg;
    output(msg);
}
```

## Value Objects

### on signal

* called as soon as a signal changes.

```c
on signal LightSwitch::OnOff
{
    v1 = this.raw;
    v2 = $LightSwitch::OnOff.raw
}
```

### on signal_update

* called with every signal reception.

### on sysvar

* The event procedure type on sysVar is provided to react to value changes of system variables in CANoe.
* In contrast to messages, system variables are not blocked by CAPL nodes in a data flow branch of the measurement configuration.
* Therefore, when there are two CAPL nodes in series, both react to the same system variable with the event procedure on sysVar.

```c
on sysvar_update dummy::sys_var_1
{
  if(@this == 1)
  {
    output(msg_es);
  }
}
```

### on sysvar_update

* called with every system variable reception

## CAN events

### on message

* The event procedure on message is called on the receipt of a valid CAN message.

| message event              | description                                                                                                |
| -------------------------- | ---------------------------------------------------------------------------------------------------------- |
| on message 123             | event triggered if message id 123 (decimal, standard identifier) received regardles of receiving chip      |
| on message 123x            | event triggered if message id 123 (decimal, extended identifier) received regardles of receiving chip     |
| on message 0x123           | event triggered if message id 123 (hexadecimal, standard identifier) received regardles of receiving chip |
| on message 0x123x          | event triggered if message id 123 (hexadecimal, extended identifier) received regardles of receiving chip |
| on message EngineData      | event triggered if message EngineData received regardles of receiving chip                               |
| on message CAN1.123        | event triggered if message id 123 (decimal, standard identifier) received on CAN1                          |
| on message *               | event triggered for all messages other than the defined message events in the same node                   |
| on message CAN2.*          | event triggered for all messages other than the defined message events in the same node on CAN2           |
| on message CAN2.[*]        | event triggered for all messages on CAN2                                                                 |
| on message 50, 60, 100-200 | event triggered for messages 50, 60, and 100 to 200                                                        |

## user defined functions

```c
void dummy_user_func()
{
    int var_1 = 10;
    write("hello from user function. valr_1 = %d", var_1)
}
```

```c
void dummy_user_func_with_params(int var_1)
{
    write("hello from user function. valr_1 = %d", var_1)
}
```

```c
int dummy_user_func_with_params_and_return(int var_1)
{
    write("hello from user function. valr_1 = %d", var_1)
    return var_1*10
}
```

## "this" keyword

* Within an event procedure for receiving a CAN object or an environment variable, the data structure of the object is designated by the keyword **this**.
* The only event procedures that can use the **this** keyword are

```c
on message
on envVar
on key
on errorframe   // only to get the CAN channel number
on busOff
on errorPassive
on errorActive
on warningLimit
```

For example, you could access the first data byte of message 100 which was just received by means of the following

```c
on message 100
{
    byte byte_0;
    byte_0 = this.byte(0);
    write("byte 0 value = %d", byte_0);
}
```

Analogously, you could read the new value of the integer environment variable Switch which has just been changed by means of the following

```c
on envVar Switch
{
    int val;
    val = getValue(this);
}
```

## Control logging block using CAPL

The startLogging() and stopLogging() functions can be used to start and stop logging.

```c
on key 'x'
{
    startLogging();
    write("Started Logging...");
}
```

```c
on key 'y'
{
    stopLogging();
    write("Stopped Logging...");
}
```

## Using CAPL for testing

![canoe_test_env](./canoe_capl_demo_cfg/doc_images/canoe_test_env.png)
![test_module](./canoe_capl_demo_cfg/doc_images/test_module.png)
![test_setup](./canoe_capl_demo_cfg/doc_images/test_setup.png)

### Test Setup

* The user-defined test setup is displayed graphically in this window.
* All options to parameterize the test environments are selected in this window.

### Test Environment

* All test environments are shown in a tree view in this window and can be configured there. Each root folder represents an independent test environment file. In CANoe there is exactly one Test Setup for Test Modules window in which several test environments can be loaded. A test environment consists of any directory structure that enables the grouping of test blocks.
* This window can be used to carry out the full range of actions, such as loading and saving, as well as creating new test environments. Right-click on the free window space and select the desired action from the context menu.
* Similarly, you can use the context menu of each respective object to carry out operations on individual test environments, folders or nodes in the test setup.
* Each test environment is stored in an individual file (*.TSE – Test Setup Environment) and can thus be loaded or unloaded independently of the CANoe configuration (simulation and analysis window).

### Test Module

* A test module contains a set of test cases that are executed sequentially by the test execution of CANoe or by its internal control program. Execution of a test module generates a test report.

### Test Case

* In a test case, a specific property of a system/unit (SUT) to be tested is tested. A test case has a clearly defined test task and returns a clear test result in the form of a verdict when it is executed.
* Test cases are only available if a test node linked in CANoe is concerned.

### Test Report

* The results from execution of a test module are recorded in a test report. A test report consists primarily of certain administrative information (such as the name of the test module, date of execution, etc.), information on the test cases executed and the results of the test. CANoe creates XML and HTML files to store test reports.
  ![img](./canoe_capl_demo_cfg/doc_images/capl_test_script.png)

## **Examples based on use cases**

### **[How to send a message manually using a keyboard ?](./canoe_capl_demo_cfg/examples/program_01.can)**

![send a message manually using a keyboard](./canoe_capl_demo_cfg/examples/program_01.png)

### **[How to send a message periodically ?](./canoe_capl_demo_cfg/examples/program_02.can)**

![program_02](./canoe_capl_demo_cfg/examples/program_02.png)

### **[How to send a message based on a system event ?](./canoe_capl_demo_cfg/examples/program_03.can)**

![program_03](./canoe_capl_demo_cfg/examples/program_03.png)

### **[How to act and react based on the graphic panel inputs ?](./canoe_capl_demo_cfg/examples/program_04.can)**

![program_04](./canoe_capl_demo_cfg/examples/program_04.png)

### **[How to create a test case to check if a signal value is valid ?](./canoe_capl_demo_cfg/examples/program_05.can)**

![program_05](./canoe_capl_demo_cfg/examples/program_05.png)

### **[How to send a diagnostic message ?](./canoe_capl_demo_cfg/examples/program_06.can)**

![program_06](./canoe_capl_demo_cfg/examples/program_06.png)

### **[How to validate diagnostic messages ?](./canoe_capl_demo_cfg/examples/program_07.can)**

![program_07](./canoe_capl_demo_cfg/examples/program_07.png)

### **[How to capture a graphic window screenshot and insert it in a report ?](./canoe_capl_demo_cfg/examples/program_08.can)**

![program_08](./canoe_capl_demo_cfg/examples/program_08.png)

### **[How to execute windows programs from a CAPL program ?](./canoe_capl_demo_cfg/examples/program_09.can)**

![program_09](./canoe_capl_demo_cfg/examples/program_09.png)
