### note: change the file path to your current path

### generate ast (compiler contract version 8 or higher)

solc --ast-compact-json file.sol > file.solast

### run the solidity file

./build/src/esbmc/esbmc --sol /home/mat/workspace/esbmc/file.sol /home/mat/workspace/esbmc/file.solast --incremental-bmc
