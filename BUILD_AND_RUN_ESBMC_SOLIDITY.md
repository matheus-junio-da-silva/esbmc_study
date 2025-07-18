### Como configurar, compilar e executar o ESBMC a partir do código-fonte para contratos em solidity

Este guia mostra como configurar o ambiente, instalar dependencia, compilar e executar o [ESBMC](https://github.com/esbmc/esbmc).

doc: https://ssvlab.github.io/esbmc/documentation.html#esbmc-solidity

---

### 1. Instalar dependências do sistema

```bash
sudo apt update
sudo apt-get install -y clang-14 llvm-14 clang-tidy-14 python-is-python3 python3 git ccache unzip wget curl \
  bison flex g++-multilib linux-libc-dev libboost-all-dev libz3-dev libclang-14-dev libclang-cpp-dev cmake
```

Esses pacotes são necessários para compilar o ESBMC, incluindo o compilador Clang/LLVM 14, ferramentas de build (como CMake), bibliotecas de desenvolvimento (Boost, Z3, libclang) e outras ferramentas de suporte.

---

### 2. Clonar o repositório do ESBMC

```bash
git clone https://github.com/esbmc/esbmc.git
cd esbmc
mkdir build && cd build
```

Clona o repositório oficial do ESBMC e cria um diretório de build separado.

---

### Criar um arquivo de swap (opcional, mas recomendado)

Se seu sistema tem pouca RAM (menos de 6 GB disponíveis), um arquivo de swap é necessário para evitar erros durante a compilação.

```bash
sudo fallocate -l 4G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

Verifique se o swap está ativo:

```bash
free -h
```

---

### 4. Compilar o ESBMC

```bash
cmake .. -DDOWNLOAD_DEPENDENCIES=ON -DENABLE_Z3=1 -DENABLE_SOLIDITY_FRONTEND=On -DBUILD_STATIC=OFF -DCMAKE_BUILD_TYPE=Debug
make -j$(nproc)
```

- `DOWNLOAD_DEPENDENCIES=ON`: baixa automaticamente as dependências externas necessarias.
- `ENABLE_Z3=1`: ativa o suporte ao solver SMT Z3.
- `ENABLE_SOLIDITY_FRONTEND=On`: ativa a análise de contratos Solidity.
- `BUILD_STATIC=OFF`: compila de forma dinâmica (mais leve para debug).

O comando `make -j$(nproc)` compila o projeto utilizando todos os núcleos disponíveis do processador. Caso começe a travar ou a dar problemas de compilacao, utilizar `make -j1`.

---

###  5. Como rodar o ESBMC com contratos Solidity

Agora que o ESBMC está compilado, siga os passos abaixo para verificar contratos Solidity:

> **Importante:** certifique-se de ajustar os paths dos arquivos para o seu ambiente atual.

---

#### 5.1 Gerar AST do contrato Solidity

>Para gerar a AST é necessário utilizar contratos escritos na versão 0.8 ou superior do Solidity.

Primeiro gere a AST (Abstract Syntax Tree) usando o `solc`:

```bash
solc --ast-compact-json contract.sol > contract.solast
```

Esse comando cria uma representação compacta da AST em JSON, que será usada pelo ESBMC durante a análise.

---

#### 5.2 Executar o ESBMC com suporte a Solidity

Com os arquivos `.sol` e `.solast` prontos, execute o ESBMC com o frontend de Solidity ativado:

```bash
./build/src/esbmc/esbmc --sol /caminho/para/file.sol /caminho/para/file.solast --incremental-bmc
```

- Substitua `/caminho/para/` pelo diretório correto onde estão seus arquivos.
- A flag `--incremental-bmc` ativa a verificação incremental, melhorando o desempenho da análise.

---

### Exemplo completo

```bash
solc --ast-compact-json contract.sol > contract.solast
./build/src/esbmc/esbmc --sol contract.sol contract.solast --incremental-bmc
```

```bash
solc --ast-compact-json integer_overflow_add.sol > integer_overflow_add.solast
./build/src/esbmc/esbmc --sol integer_overflow_add.sol integer_overflow_add.solast --incremental-bmc --overflow-check
```

```bash
cmake .. \
  -DDOWNLOAD_DEPENDENCIES=ON \
  -DENABLE_Z3=1 \
  -DENABLE_SOLIDITY_FRONTEND=On \
  -DBUILD_STATIC=OFF \
  -DCMAKE_BUILD_TYPE=Debug \
  -DLLVM_DIR=/usr/lib/llvm-16/cmake \
  -DClang_DIR=/usr/lib/llvm-16/cmake \
  -DOVERRIDE_CLANG_HEADER_DIR=/usr/lib/llvm-16/lib/clang/16/include
```

```bash
sudo apt install llvm-16-dev clang-16 libclang-16-dev
```
## TEM QUE USAR ESSA PARA SOLIDITY, TODAS VERSOES 16
```bash
sudo apt update
sudo apt-get install -y clang-16 llvm-16 clang-tidy-16 python-is-python3 python3 git ccache unzip wget curl bison flex g++-multilib linux-libc-dev libboost-all-dev libz3-dev libclang-16-dev libclang-cpp-dev cmake
```

```bash
sudo add-apt-repository ppa:ethereum/ethereum
sudo apt update
sudo apt install solc
```

```bash
https://github.com/esbmc/esbmc.git
```

```bash
cd esbmc
mkdir build && cd build
```

```bash
sudo fallocate -l 4G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

```bash
rm -rf build
mkdir build
cd build
```

```bash
cmake .. -DENABLE_Z3=1 -DENABLE_SOLIDITY_FRONTEND=On -DCMAKE_BUILD_TYPE=Debug
```

```bash
cmake .. -DENABLE_Z3=1 -DENABLE_SOLIDITY_FRONTEND=On -DCMAKE_BUILD_TYPE=Debug -DBUILD_TESTING=Off -DENABLE_REGRESSION=Off
```

```bash
make -j2
```

```bash
./build/src/esbmc/esbmc --sol integer_overflow_add.sol integer_overflow_add.solast --incremental-bmc --overflow-check
```

 ### pode ser util

```bash
cd /home/mat/esbmc && ./build/src/esbmc/esbmc --sol simple_overflow.sol simple_overflow.solast --incremental-bmc --overflow-check > debug_output.log 2>&1 && cat debug_output.log
```
