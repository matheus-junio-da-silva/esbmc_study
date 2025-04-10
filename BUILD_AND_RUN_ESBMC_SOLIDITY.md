### Como configurar, compilar e executar o ESBMC a partir do código-fonte para contratos em solidity

Este guia mostra como configurar o ambiente, instalar dependencia, compilar e executar o [ESBMC](https://github.com/esbmc/esbmc).

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



