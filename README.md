# Create_Thread: Inline_Assembly_x86
This POC provides the ability to execute x86 shellcode in the form of a .bin file based on x86 inline assembly.
The POC includes a calc.bin, which executes the Windows x86 calculator and can be replaced for example by a meterpreter .bin payload.

# **Disclaimer**
The content and all code examples in this article are for research purposes only and must not be used in an unethical context! The code used is not new and I make no claim to it. Most of the code comes, as so often, from the **ired.team, thank you @spotheplanet for your brilliant work and sharing it with us all!**

# **Description**
This code initializes a handle (HRSRC) to a binary resource in the executable file. The FindResource() function locates the resource based on its name (IDR_CALC_BIN1) and type (CALC_BIN). The NULL parameter specifies the module instance handle, which is not needed in this case since the resource is located in the same executable file.
```// Load the resource
    HRSRC codeResource;
    codeResource = FindResource(NULL, MAKEINTRESOURCE(IDR_CALC_BIN1), L"CALC_BIN");```
    
