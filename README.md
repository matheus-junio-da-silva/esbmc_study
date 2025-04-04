# esbmc_study

cmake .. -DENABLE_Z3=1 \
  -DOVERRIDE_CLANG_HEADER_DIR=/usr/lib/llvm-14/lib/clang/14.0.6/include \
  -DLLVM_DIR=/usr/lib/llvm-14/lib/cmake/llvm


Dessa forma, tanto o LLVM quanto o Clang apontarão para a versão 14, eliminando o conflito.


#  reduce the number of parelellism

make -j1

# run

~/workspace/ESBMC_Project/esbmc$ ./build/src/esbmc/esbmc /home/mat/workspace/ESBMC_Project/esbmc/file.c --incremental-bmc

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



