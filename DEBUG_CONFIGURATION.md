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
