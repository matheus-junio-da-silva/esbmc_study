Preciso entender como os padrões de vulnerabilidades são definidos e verificados em contratos Solidity. Por exemplo, no caso de overflow, quero saber qual é o padrão utilizado para identificá-lo: o que exatamente o analisador (ou ferramenta de verificação) procura no código do contrato para caracterizar a presença de uma vulnerabilidade de overflow? Além disso, como essas propriedades e padrões são gerados, formalizados e verificados internamente pela ferramenta?

visão geral da ferramenta explicada nesse trecho:

2 Tool description
2.1 Tool Overview
Fig. 1 illustrates the architecture of ESBMC-Solidity, where
the gray box with solid border represents the new frontend,
and the white ones constitute the existing ESBMC’s components. The gray box with a dashed border indicates an
external element for preprocessing smart contracts: the Solidity compiler. It is used for lexical analysis and parsing, taking
a smart contract as input and then transforming it into JSON
AST, which is done with the argument --ast-compact-json.
The proposed approach takes JSON AST and converts each
of its nodes into an equivalent IR one, using the ESBMC’s
irept, a tree-structured IR that preserves a program’s semantics.Next, each irept node is converted into the corresponding
symbol and then added to a table, which is translated into
a GOTO program. Then, the latter is processed by the symbolic execution engine (SymEx) to generate its static single
assignment (SSA) form, which is used to generate verification
condition (VCs) 𝐶 ∧ ¬𝑃, where 𝐶 represents constraints and
𝑃 denotes a safety property. Lastly, ESBMC uses off-the-shelf
SMT solvers for verifying the satisfiability of those VCs.
If a property is satisfiable, an execution path leads to a bug
in an original Solidity smart contract. Then, when ESBMC
detects it, a counterexample is provided, in the form of state
traces, to allow its reproduction. It is worth noticing that
ESBMC supports several SMT solvers, including Z3, Bitwuzla,
Boolector, MathSAT, CVC4, and Yices [17].
2.2 The Grammar-Based Hybrid Conversion
Methodology
Given an input smart contract, the goal of the proposed frontend is to populate the resulting symbol table, where each
symbol is represented by the ESBMC’s symbolt data structure [17]. Furthermore, it shall complete the type-checking
procedure of Solidity AST nodes and transform each JSON
AST node into its equivalent ESBMC’s irept one while preserving the associated semantic information. To achieve this
goal, we developed this frontend based on the GrammarBased Hybrid Conversion methodology, as an approach specifically devised for that during the development of this work.
Grammar-Based Conversion. The proposed frontend
uses the library nlohmann/json1
to process Solidity ASTs in
JSON format. When traversing Solidity AST nodes, it uses
different functions to transform them into equivalent 𝑖𝑟𝑒𝑝_𝑡
ones, e.g., get_var_decl_stmt, get_expr, and get_statement,
for variable-declaration-statement, expression, and statement
nodes, respectively. Besides, each AST node may contain multiple child ones, e.g., the AST node of a for loop contains four
child nodes: initialisation, condition, increment, and loop body.
So, during their conversion, the production rules in Solidity
grammar documentation2
are followed, so that they are visited in correct order. For instance, the variable-initialisation
node of a for loop must be visited before the body one, as it
may be referenced by the latter. If the node for a body loop is
converted before its variable initialisation, the type checker
will fail to handle any reference to it.
Hybrid conversion. Three functions must be supported:
(1) 𝑎𝑠𝑠𝑒𝑟𝑡() for defining safety properties; (2) 𝑎𝑠𝑠𝑢𝑚𝑒 () for
defining constraints; and (3) 𝑛𝑜𝑛𝑑𝑒𝑡() for assigning nondeterministic values to variables. Consequently, they are
implemented by ESBMC as C-style declarations. However,
the new frontend works with JSON AST nodes. Besides, since
1
JSON for Modern C++ - https://github.com/nlohmann/json
2Solidity Grammar - https://docs.soliditylang.org/en/v0.8.6/grammar.html

ESBMC-Solidity: An SMT-Based Model Checker for Solidity Smart Contracts Conference’22, May 2022, Pittsburgh, PA, USA
there are more than 70 intrinsic declarations, e.g., forward
declarations for nondeterministic types, we instantiate the
existing ESBMC’s clang frontend to convert those into irept
nodes to avoid replication, hence generating the symbol table
mentioned before. Finally, the latter is further merged with
the symbol table generated from the original Solidity AST.
2.3 Illustrative Example
1 pragma solidity >=0.4.26;
2 contract MyContract {
3 uint8 x ;
4 uint8 sum ;
5 function nondet () public pure
6 returns ( uint8 ) {
7 uint8 i ;
8 return i ;
9 }
10 function __ESBMC_assume ( bool )
11 internal pure { }
12 function func_sat () external {
13 x = 0;
14 uint8 y = nondet () ;
15 sum = x + y ;
16 __ESBMC_assume ( y < 255) ;
17 __ESBMC_assume ( y > 220) ;
18 __ESBMC_assume ( y != 224) ;
19 assert ( sum % 16 != 0) ;
20 }
21 }
Figure 2. An example smart contract written in Solidity.
Fig. 2 shows an example of smart-contract verification
with ESBMC-Solidity. Indeed, developers can instrument
code, e.g., nondet for nondeterministic integers between 0
and 255 and __ESBMC_assume for additional constraints.
Those help developers narrow down the scope for triggering
a bug, hence identifying a set of breaking inputs. Function
func_sat is the one we need to verify, where two state variables x and sum are defined in lines 3 and 4, respectively,
while a safety property indicates that x + y should not be
a multiple of 16. In addition, constraints are added using
__ESBMC_assume, in lines 16, 17, and 18, which restrict 𝑦 as
any integer between 220 and 255, but 224. Then, ESBMC will
check whether there exists an execution path that satisfies
its negation. This way, the verification of func_sat becomes a
satisfiability problem: given the binary operation expression
“sum = x + y”, where x is 0 and y is a constrained nondeterministic value, find an execution state where the negation
of “𝑠𝑢𝑚%16! = 0” is satisfied. ESBMC is then invoked with
esbmc <JSON AST> --function func_sat --z3.
For the smart contract in Fig. 2, ESBMC generates the 𝐶
and 𝑃 equations as described in (1) for constraints and (2) for
property. Eq. (1) shows a conjunction of the constraints and
assignments. When generating its SSA form, ESBMC uses
the temporary variable temp to represent the left-hand-side
of the safety property specified in line 19, which corresponds
to the assignment 𝑡𝑒𝑚𝑝 = 𝑠𝑢𝑚%16 in Eq. 1. The resulting VC
for satisfiability verification, via SMT solver, is then formed
by𝐶∧¬𝑃. Consequently, ESBMC reports a property violation
and provides a counterexample that contains a trace of states
showing the set of assignments and the breaking values that
trigger such violation, where 𝑦 is set with a value 240.
𝐶 =







𝑦 = 𝑛𝑜𝑛𝑑𝑒𝑡()
∧ 𝑠𝑢𝑚 = 𝑦
∧𝑦 ! = 224
∧ 𝑡𝑒𝑚𝑝 = 𝑠𝑢𝑚%16







(1) 𝑃 =

𝑡𝑒𝑚𝑝 ! = 0

(2)

.sol smart contract
    --> Solidity Compiler (Lexical Analysis + Parsing)
        --> .JSON AST
            --> solidity-frontend (Semantic Analysis)
                --> Symbol Table
                    --> GOTO Converter
                        --> GOTO program
                            --> SymEx
                                --> SSA form
                                    --> Logical Formula Generator
                                        --> C ∧ ¬P (constraints and negated property)
                                            --> SMT solver
                                                --> [If no property violation (up to bound k)] --> Verification Successful
                                                --> [If property violation] --> Counterexample
 

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















nesse contexto qual a diferença de constraints para propriedades? a propiedade seria algo que deve ser respeitado em todo contrato solidity, e as constraints seriam as regras do contrato em si, ou seja o comportamento dele?

# Constraints vs Propriedades em Verificação Formal de Contratos Solidity

Você identificou uma distinção importante no contexto de verificação formal. Vamos esclarecer essas diferenças:

## Constraints (Restrições)

As constraints representam **premissas** ou **condições de entrada** que delimitam o escopo da verificação:

```solidity
// C : Add additional constraints here
__ESBMC_assume(y < 255);
__ESBMC_assume(y > 220);
__ESBMC_assume(y != 224); // 224 = 16 * 14;
__ESBMC_assume(y != 240); // 240 = 16 * 15;
```

Características das constraints:
- São **assumidas como verdadeiras** pelo verificador
- Restringem o espaço de estados que será explorado
- Representam condições sob as quais o contrato deve operar
- No ESBMC-Solidity, são implementadas usando `__ESBMC_assume()`
- Não são verificadas, mas sim usadas para limitar o escopo da análise

## Propriedades (Properties)

As propriedades são **afirmações** que devem ser verdadeiras sob as constraints estabelecidas:

```solidity
// P : Properties we want to check
assert(sum % 16 != 0);
```

Características das propriedades:
- São o alvo da verificação formal
- Definem o que deve ser garantido pelo contrato
- São implementadas usando `assert()`
- O verificador tenta provar que são sempre verdadeiras (ou encontrar um contraexemplo)

## Relação entre Constraints e Propriedades

No contexto da verificação formal com ESBMC-Solidity:

- **Constraints** (C): Definem as condições ou contexto sob o qual o contrato vai operar
- **Propriedades** (P): Definem os requisitos que devem ser satisfeitos quando o contrato opera dentro dessas condições

O verificador tenta resolver: "Existe algum estado que satisfaça todas as constraints C mas viole a propriedade P?"

Em termos lógicos, o verificador verifica a satisfabilidade de: C ∧ ¬P

## No Contexto de Contratos Solidity

- **Constraints** representam:
  - Regras de negócio do contrato
  - Limites de entrada de parâmetros
  - Comportamento esperado de variáveis

- **Propriedades** representam:
  - Invariantes de segurança
  - Garantias que o contrato deve manter
  - Características que devem ser verdadeiras para todos os estados válidos

Você está correto em sua interpretação: as constraints descrevem o comportamento ou regras do contrato, enquanto as propriedades são garantias que devem ser respeitadas em todas as execuções válidas.
```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.4.26;

contract MyContract {
    uint8 x;
    uint8 sum;

    function nondet() public pure returns(uint8)
    {
      uint8 i;
      return i;
    }

    function __ESBMC_assume(bool) internal pure { }

    function func_sat() external {
      x = 0;
      uint8 y = nondet();
      sum = x + y;

      // C : Add additional constraints here
      __ESBMC_assume(y < 255);
      __ESBMC_assume(y > 220);
      __ESBMC_assume(y != 224); // 224 = 16 * 14;
      __ESBMC_assume(y != 240); // 240 = 16 * 15;

      // P : Properties we want to check
      assert(sum % 16 != 0);
    }
}

```













Eu queria saber se o ESBMC também consegue detectar vulnerabilidades em contratos inteligentes, de tal forma que eu não precise passar nenhum tipo de propriedade ou constraint para ele, porque eu estava vendo que no artigo deles, eles utilizavam propriedades e constraint escritas no próprio contrato. Bem, mas eu queria algo mais automático, sabe? Eu também queria saber se ele consegue identificar vulnerabilidades de, por exemplo, overflow, sem ter que passar uma propriedade, entende?


# Verificação Automática de Vulnerabilidades em Contratos Solidity com ESBMC

Sim, o ESBMC tem capacidade para detectar automaticamente vulnerabilidades em contratos inteligentes, incluindo overflows, sem a necessidade de você especificar propriedades manualmente em cada análise.

## Verificação Automática com Propriedades Pré-definidas

O ESBMC-Solidity oferece análise automática através do uso de flags específicas:

```bash
esbmc arquivo.sol --overflow-check
```

Usando a flag `--overflow-check`, o ESBMC automaticamente:

1. Insere verificações implícitas para todas as operações aritméticas
2. Detecta problemas como:
   - Integer overflow/underflow
   - Divisão por zero
   - Conversões de tipo inseguras

Você não precisa adicionar nenhuma assertion ou constraint no seu código - as verificações são inseridas automaticamente durante a análise.

## Como Funciona Internamente

Quando você executa o ESBMC com `--overflow-check`:

1. O ESBMC converte o contrato Solidity para sua representação interna
2. Automaticamente instrumenta o programa com verificações em cada operação aritmética
3. Gera condições de verificação (VCs) que representam estas propriedades implícitas
4. Envia estas condições para um solver SMT para verificar se existe alguma violação

## Exemplo Prático

Por exemplo, para verificar automaticamente um contrato para vulnerabilidades de overflow:

```bash
esbmc MeuContrato.sol --overflow-check --function transferir
```

Esta abordagem é mais automática do que a mostrada no artigo, onde você tinha que adicionar:

```solidity
assert(sum % 16 != 0);
```

## Verificação de Múltiplas Propriedades

Para uma verificação ainda mais abrangente, você pode combinar flags:

```bash
esbmc MeuContrato.sol --overflow-check --div-by-zero-check --multi-property
```

A flag `--multi-property` permite que o ESBMC verifique múltiplas propriedades em uma única execução, tornando o processo mais eficiente.

## Conclusão

O ESBMC-Solidity oferece sim detecção automática de vulnerabilidades comuns em contratos inteligentes, sem a necessidade de especificar manualmente propriedades ou constraints. Para casos específicos ou propriedades de negócio customizadas, você sempre pode adicionar `assert()` manualmente, mas para vulnerabilidades padrão como overflow, as flags de verificação automática são suficientes.


```solidity
/*
 * @source: https://github.com/ConsenSys/evm-analyzer-benchmark-suite/blob/master/benchmarks/integer_overflow_add.sol
 * @author: -
 * @vulnerable_at_lines: 17
 */

//Single transaction overflow
//Post-transaction effect: overflow escapes to publicly-readable storage

pragma solidity ^0.8.0;

contract IntegerOverflowAdd {
    uint public count = 1;

    function run(uint256 input) public {
        // <yes> <report> ARITHMETIC
        count += input;
    }
}
```

```bash
mat@matheusjunio:~/workspace/esbmc$ ./build/src/esbmc/esbmc --sol /home/mat/workspace/esbmc/integer_overflow_add.sol /home/mat/workspace/esbmc/integer_overflow_add.solast --incremental-bmc --overflow-check
ESBMC version 7.8.1 64-bit x86_64 linux
Target: 64-bit little-endian x86_64-unknown-linux with esbmclibc
Parsing /home/mat/workspace/esbmc/integer_overflow_add.solast
Converting
Generating GOTO Program
GOTO program creation time: 0.310s
GOTO program processing time: 0.005s
Checking base case, k = 1
Starting Bounded Model Checking
Not unwinding loop 51 iteration 1   
Symex completed in: 0.004s (72 assignments)
Slicing time: 0.000s (removed 51 assignments)
Generated 18 VCC(s), 8 remaining after simplification (21 assignments)
No solver specified; defaulting to z3
Encoding remaining VCC(s) using bit-vector/floating-point arithmetic
Encoding to solver time: 0.001s
Solving with solver Z3 v4.13.3
Runtime decision procedure: 0.027s
Building error trace

[Counterexample]


State 7 file integer_overflow_add.sol line 17 function run thread 0
----------------------------------------------------
Violated property:
  file integer_overflow_add.sol line 17 function run
  arithmetic overflow on add
  !overflow("+", this->count, input)


VERIFICATION FAILED

Bug found (k = 1)
mat@matheusjunio:~/workspace/esbmc$
```







