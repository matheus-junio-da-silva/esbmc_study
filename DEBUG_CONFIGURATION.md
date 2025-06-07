### launch.json

> **importante:** Trocar os caminhos

```json
{
    "version": "0.2.0",
    "configurations": [
      {
        "name": "Debug ESBMC",
        "type": "cppdbg",
        "request": "launch",
        "program": "${workspaceFolder}/build/src/esbmc/esbmc",
        "args": [
          "--symex-trace",
          "--symex-ssa-trace",
          "--sol",
          "${workspaceFolder}/contract.sol",
          "contract.solast",
          "--incremental-bmc"
        ],
        "stopAtEntry": false,
        "cwd": "${workspaceFolder}",
        "environment": [],
        "externalConsole": false,
        "MIMode": "gdb",
        "miDebuggerPath": "/usr/bin/gdb",
        "setupCommands": [
          {
            "description": "Habilita a impressão de variáveis para o GDB",
            "text": "-enable-pretty-printing",
            "ignoreFailures": true
          }
        ]
      }
    ]
  }
```


```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Debug ESBMC with Enhanced Variable Display",
      "type": "cppdbg",
      "request": "launch",
      "program": "${workspaceFolder}/build/src/esbmc/esbmc",
      "args": [
        "--symex-trace",
        "--symex-ssa-trace",
        "--sol",
        "${workspaceFolder}/integer_overflow_add.sol",
        "${workspaceFolder}/integer_overflow_add.solast",
        "--overflow-check"
      ],
      "stopAtEntry": false,
      "cwd": "${workspaceFolder}",
      "environment": [],
      "externalConsole": false,
      "MIMode": "gdb",
      "miDebuggerPath": "/usr/bin/gdb",
      "setupCommands": [
        {
          "description": "Enable pretty-printing for gdb",
          "text": "-enable-pretty-printing",
          "ignoreFailures": false
        },
        {
          "description": "Set disassembly flavor to Intel",
          "text": "-gdb-set disassembly-flavor intel",
          "ignoreFailures": true
        },
        {
          "description": "Print array elements",
          "text": "-gdb-set print elements unlimited",
          "ignoreFailures": false
        },
        {
          "description": "Show string contents",
          "text": "-gdb-set print characters 100",
          "ignoreFailures": false
        },
        {
          "description": "Pretty print structures",
          "text": "-gdb-set print pretty on",
          "ignoreFailures": false
        },
        {
          "description": "Enable STL pretty printing",
          "text": "python import sys; sys.path.insert(0, '/usr/share/gcc/python'); from libstdcxx.v6.printers import register_libstdcxx_printers; register_libstdcxx_printers(None)",
          "ignoreFailures": true
        }
      ],
      //preLaunchTask": "build-debug"
    }
  ]
}

```

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Debug ESBMC with Enhanced Variable Display",
      "type": "cppdbg",
      "request": "launch",
      "program": "${workspaceFolder}/build/src/esbmc/esbmc",
      "args": [
        "--symex-trace",
        "--symex-ssa-trace",
        "--sol",
        "${workspaceFolder}/integer_overflow_add.sol",
        "${workspaceFolder}/integer_overflow_add.solast",
        "--overflow-check"
      ],
      "stopAtEntry": false,
      "cwd": "${workspaceFolder}",
      "environment": [],
      "externalConsole": false,
      "MIMode": "gdb",
      "miDebuggerPath": "/usr/bin/gdb",
      "setupCommands": [
        {
          "description": "Enable pretty-printing for gdb",
          "text": "-enable-pretty-printing",
          "ignoreFailures": false
        }
    }
  ]
}

```
