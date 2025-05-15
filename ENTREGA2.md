# Detecção de Vulnerabilidades em Contratos Solidity com ESBMC-Solidity

## Como as Vulnerabilidades de Overflow São Formalizadas e Detectadas

O ESBMC-Solidity utiliza verificação formal baseada em SMT (Satisfiability Modulo Theories) para identificar vulnerabilidades em contratos inteligentes. Vou explicar como as vulnerabilidades, especialmente overflows, são definidas, formalizadas e verificadas.

### 1. Definição de Padrões de Vulnerabilidade

Para vulnerabilidades de overflow aritmético, o modelo formal se baseia nas limitações dos tipos numéricos do Solidity:

- Uma operação de adição como `sum = x + y` em um tipo `uint8` sofre overflow quando o resultado excede 255
- Isso é formalizado como uma violação da propriedade implícita: `!(x + y > 255)`

O ESBMC-Solidity define estas vulnerabilidades como padrões de violação de **propriedades de segurança** que são verificadas durante a análise do contrato.

### 2. Processo de Verificação de Overflow

Analisando o exemplo fornecido:

```solidity
function func_sat() external {
  x = 0;
  uint8 y = nondet();
  sum = x + y;
  
  __ESBMC_assume(y < 255);
  __ESBMC_assume(y > 220);
  __ESBMC_assume(y != 224);
  
  assert(sum % 16 != 0);
}
```

O processo interno de verificação ocorre da seguinte forma:

1. **Tradução para GOTO**: O contrato é convertido em um programa GOTO intermediário
2. **Adição de Verificações**: São inseridas verificações implícitas para cada operação aritmética
3. **Geração da Forma SSA**: O programa é convertido para Static Single Assignment
4. **Formulação Lógica**: As condições de verificação são expressas como fórmulas lógicas

### 3. Formalização Matemática

Para o exemplo acima, o ESBMC gera as seguintes fórmulas:

**Constraints (C)**:
```
C = (y = nondet() ∧ sum = y ∧ y < 255 ∧ y > 220 ∧ y != 224 ∧ temp = sum % 16)
```

**Propriedade (P)**:
```
P = (temp != 0)
```

A verificação resolve a satisfabilidade de `C ∧ ¬P`, ou seja, verifica se existe um estado que satisfaz as constraints e viola a propriedade.

### 4. Detecção Interna

Internamente, o fluxo de detecção ocorre em várias etapas:

1. O frontend do ESBMC-Solidity converte a AST JSON do contrato para a representação interna `irept`
2. Durante esta conversão, são identificadas operações potencialmente vulneráveis
3. Para cada operação aritmética em tipos limitados (como `uint8`), são geradas:
   - **Constraints** representando o comportamento normal do programa
   - **Propriedades** que verificam a ausência de overflow (`valor ≤ MAX_VALUE`)

4. O motor de execução simbólica explora todos os caminhos possíveis de execução
5. O solver SMT verifica se existe algum caminho que viole as propriedades definidas

### 5. Exemplo Concreto - Detecção de Overflow

Para a operação `sum = x + y` em `uint8`:

1. O ESBMC identifica que esta é uma operação de adição em um tipo com limite
2. É gerada uma verificação implícita: `assert(x + y <= 255)` (limite máximo de `uint8`)
3. Se o solver encontrar valores onde `x + y > 255`, é reportado um overflow

No exemplo fornecido, a ferramenta encontra que quando `y = 240`, a propriedade `sum % 16 != 0` é violada, pois `240 % 16 = 0`, gerando um contraexemplo.

### 6. Geração de Contraexemplos

Quando uma vulnerabilidade é detectada, o ESBMC-Solidity gera um contraexemplo que inclui:
- Os valores concretos que causam a violação
- O caminho de execução completo até o ponto da vulnerabilidade
- O estado do contrato no momento da violação

Isso permite que os desenvolvedores reproduzam e corrijam a vulnerabilidade encontrada.

## Conclusão

O ESBMC-Solidity formaliza vulnerabilidades como overflow através da tradução de operações aritméticas para fórmulas lógicas verificáveis. A combinação de execução simbólica e resolvedores SMT permite uma análise rigorosa e exaustiva, detectando vulnerabilidades que podem não ser encontradas por testes tradicionais.
