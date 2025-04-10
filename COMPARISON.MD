# Comparativo: Mythril vs ESBMC

##  Visão Geral

| Ferramenta | Foco Principal | 
|------------|----------------|
| **Mythril** | Contratos inteligentes na Ethereum |
| **ESBMC**   | Verificação formal de software C/C++ |

---

## Técnicas Utilizadas

| Ferramenta | Técnicas Internas | Nível de operação | Análise |
|------------|-------------------|-------------------|---------|
| **Mythril** | Execução simbólica + SMT solver (Z3) + Taint Analysis | Bytecode da EVM | Estática + dinâmica
| **ESBMC**   | Bounded Model Checking (BMC) + SMT solvers | Código fonte | Estática

---


