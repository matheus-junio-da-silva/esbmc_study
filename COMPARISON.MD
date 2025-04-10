# Comparativo: Mythril vs ESBMC

## 🧩 Visão Geral

| Ferramenta | Foco Principal | Linguagens Suportadas |
|------------|----------------|------------------------|
| **Mythril** | Segurança de contratos inteligentes na Ethereum | Solidity (bytecode EVM) |
| **ESBMC**   | Verificação formal de software C/C++ e sistemas embarcados | C, C++ (e Solidity via integração) |

---

## 🛠️ Técnicas Utilizadas

| Ferramenta | Técnicas Internas |
|------------|-------------------|
| **Mythril** | Execução simbólica + SMT solver (Z3) + heurísticas |
| **ESBMC**   | Bounded Model Checking (BMC) + SMT solvers (Z3, Boolector, etc.) |

---

## ✅ Propriedades Verificadas

| Propriedade                                    | Mythril | ESBMC |
|------------------------------------------------|:-------:|:-----:|
| Overflow/Underflow de inteiros                 |   ✅    |  ✅   |
| Reentrância (Reentrancy)                       |   ✅    |  ❌   |
| Funções não protegidas                         |   ✅    |  ❌   |
| Acesso inválido à memória                      |   ❌    |  ✅   |
| Estouro de limites de arrays                   |   ❌    |  ✅   |
| Assertivas e invariantes                       |   ✅    |  ✅   |
| Verificação de propriedades temporais (CTL/LTL)|   ❌    |  ✅   |
| Deadlocks em código concorrente                |   ❌    |  ✅   |
| Dependência de timestamp / bloco               |   ✅    |  ❌   |
| Análise de tempo real (RT)                     |   ❌    |  ✅   |
| Retornos não verificados em chamadas externas  |   ✅    |  ❌   |
| Consumo de gás anômalo                         |   ✅    |  ❌   |

---

## 📌 Conclusão

- **Mythril** é ideal para encontrar **vulnerabilidades específicas de contratos inteligentes Ethereum**.
- **ESBMC** é mais geral e poderosa para programas **C/C++**, especialmente em **sistemas críticos e embarcados**.
