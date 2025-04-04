# esbmc_study

sudo apt update
sudo apt-get install -y clang-14 llvm-14 clang-tidy-14 python-is-python3 python3 git ccache unzip wget curl bison flex g++-multilib linux-libc-dev libboost-all-dev libz3-dev libclang-14-dev libclang-cpp-dev cmake
git clone https://github.com/esbmc/esbmc.git
cd esbmc
mkdir build && cd build
cmake .. -DENABLE_Z3=1 \
-DENABLE_SOLIDITY_FRONTEND=On
make -j$(nproc)

cmake .. -DENABLE_Z3=1 \
  -DOVERRIDE_CLANG_HEADER_DIR=/usr/lib/llvm-14/lib/clang/14.0.6/include \
  -DLLVM_DIR=/usr/lib/llvm-14/lib/cmake/llvm 


Dessa forma, tanto o LLVM quanto o Clang apontarão para a versão 14

cmake .. -DENABLE_Z3=1 \
  -DOVERRIDE_CLANG_HEADER_DIR=/usr/lib/llvm-16/lib/clang/16/include \
  -DLLVM_DIR=/usr/lib/llvm-16/lib/cmake/llvm  \
  -DENABLE_SOLIDITY_FRONTEND=On

#  reduce the number of parelellism

make -j1
make -j$(nproc)

# run .C

 ./build/src/esbmc/esbmc /home/mat/workspace/esbmc/file.c --incremental-bmc

# for solidity support

cmake .. -DENABLE_SOLIDITY_FRONTEND=On

# help

./build/src/esbmc/esbmc --help | grep sol

# solidity terminal options

Solidity frontend:
  --sol path                            .sol and .solast file names
  --contract cname                      set contract name
  --no-visibility                       force to verify every function, even 
                                        it's an unreachable internal/private 
                                        function
  --unbound                             model external function calls as 
                                        arbitrary behavior
  --bound                               model inter-contract function calls 
                                        within a bounded system (default)
  --negating-property fname             convert the assert(cond) to 
                                        assert(!cond)


# file.c

#include <stdlib.h>
int *a, *b;
int n;
#define BLOCK_SIZE 128
void foo () {
  int i;
  for (i = 0; i < n; i++)
    a[i] = -1;
  for (i = 0; i < BLOCK_SIZE - 1; i++)
    b[i] = -1;
}
int main () {
  n = BLOCK_SIZE;
  a = malloc (n * sizeof(*a));
  b = malloc (n * sizeof(*b));
  *b++ = 0;
  foo ();
  if (b[-1])
  { free(a); free(b); }
  else
  { free(a); free(b); }
  return 0;
}

# solidity file

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract DivisionByZero {
    uint256 public result;

    function divide(uint256 a, uint256 b) public {
        result = a / b; // <-- vulnerável se b == 0
    }
}


# generate ast (compiler contract version 8 or higher)

solc --ast-compact-json file.sol > file.solast

# run the solidity file

  ./build/src/esbmc/esbmc --sol /home/mat/workspace/esbmc/file.sol /home/mat/workspace/esbmc/file.solast --incremental-bmc

# Create a 4GB swap file
sudo fallocate -l 4G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Check if it's working
free -h

# for build
cd ~/workspace/esbmc
rm -rf build
mkdir build && cd build
cmake .. -DDOWNLOAD_DEPENDENCIES=ON -DENABLE_Z3=1 -DENABLE_SOLIDITY_FRONTEND=On -DBUILD_STATIC=OFF
make -j$(nproc)

  



