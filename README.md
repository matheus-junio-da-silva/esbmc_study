# esbmc_study

cmake .. -DENABLE_Z3=1 \
  -DOVERRIDE_CLANG_HEADER_DIR=/usr/lib/llvm-14/lib/clang/14.0.6/include \
  -DLLVM_DIR=/usr/lib/llvm-14/lib/cmake/llvm


Dessa forma, tanto o LLVM quanto o Clang apontarão para a versão 14, eliminando o conflito.


# Reduzir o Número de Paralelismo

make -j1


