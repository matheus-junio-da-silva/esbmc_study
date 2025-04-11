# Propiedades geradas pelas ferramentas Mythril e ESBMC

#### contrato de exemplo utilizado:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract AssertionExample {
    uint256 public value;

    function set(uint256 x) public {
        value = x;
        assert(x < 100); // <-- assert que pode falhar
        value = x + 1;    // <-- código inalcançável se assert falhar
    }
}

```

### Comparação entre as ferramentas

O ESBMC trabalha em um nível mais próximo do código fonte, enquanto o Mythril trabalha no nível do bytecode da EVM

### ESBMC:

Print da instância do solver no momento antes da detecção (check) da vulnerabilidade:  

> É possível verificar a satisfatibilidade através desse site (só copiar e colar a instância do solver) : https://jfmc.github.io/z3-play/

> OBS: As propriedades são os asserts

#### Instância do solver:
```
; 
(set-info :status unknown)
(declare-datatypes ((struct_type_addr_space_type 0)) (((struct_type_addr_space_type (start (_ BitVec 64)) (end (_ BitVec 64))))))
(declare-datatypes ((struct_type_pointer_struct 0)) (((struct_type_pointer_struct (pointer_object (_ BitVec 64)) (pointer_offset (_ BitVec 64))))))
(declare-fun __ESBMC_ptr_obj_start_0 () (_ BitVec 64))
(declare-fun __ESBMC_ptr_obj_end_0 () (_ BitVec 64))
(declare-fun __ESBMC_ptr_obj_start_1 () (_ BitVec 64))
(declare-fun __ESBMC_ptr_obj_end_1 () (_ BitVec 64))
(declare-fun __ESBMC_ptr_addr_range_0 () struct_type_addr_space_type)
(declare-fun __ESBMC_ptr_addr_range_1 () struct_type_addr_space_type)
(declare-fun __ESBMC_addrspace_arr_2 () (Array (_ BitVec 64) struct_type_addr_space_type))
(declare-fun __ESBMC_addrspace_arr_1 () (Array (_ BitVec 64) struct_type_addr_space_type))
(declare-fun __ESBMC_addrspace_arr_3 () (Array (_ BitVec 64) struct_type_addr_space_type))
(declare-fun NULL () struct_type_pointer_struct)
(declare-fun INVALID () struct_type_pointer_struct)
(declare-fun |sol:@C@AssertionExample@F@_ESBMC_Main_AssertionExample#::$tmp::return_value$_nondet_bool$1?1!0&0#1| () Bool)
(declare-fun |nondet$symex::nondet5| () Bool)
(declare-fun |sol:@C@AssertionExample@_ESBMC_Nondet_Extcall_AssertionExample#::$tmp::return_value$_nondet_bool$1?1!0&0#1| () Bool)
(declare-fun |nondet$symex::nondet6| () Bool)
(declare-fun |sol:@C@AssertionExample@F@set@x#5&0#0| () (_ BitVec 256))
(declare-fun |execution_statet::\\guard_exec?0!0| () Bool)
(assert
 (= __ESBMC_ptr_obj_start_0 (_ bv0 64)))
(assert
 (= __ESBMC_ptr_obj_end_0 (_ bv0 64)))
(assert
 (= __ESBMC_ptr_obj_start_1 (_ bv1 64)))
(assert
 (= __ESBMC_ptr_obj_end_1 (_ bv18446744073709551615 64)))
(assert
 (let ((?x17 (struct_type_addr_space_type __ESBMC_ptr_obj_start_0 __ESBMC_ptr_obj_end_0)))
 (= __ESBMC_ptr_addr_range_0 ?x17)))
(assert
 (let ((?x20 (struct_type_addr_space_type __ESBMC_ptr_obj_start_1 __ESBMC_ptr_obj_end_1)))
 (= __ESBMC_ptr_addr_range_1 ?x20)))
(assert
 (let ((?x17 (struct_type_addr_space_type __ESBMC_ptr_obj_start_0 __ESBMC_ptr_obj_end_0)))
 (let ((?x25 (store __ESBMC_addrspace_arr_1 (_ bv0 64) ?x17)))
 (= ?x25 __ESBMC_addrspace_arr_2))))
(assert
 (let ((?x20 (struct_type_addr_space_type __ESBMC_ptr_obj_start_1 __ESBMC_ptr_obj_end_1)))
 (let ((?x28 (store __ESBMC_addrspace_arr_2 (_ bv1 64) ?x20)))
 (= ?x28 __ESBMC_addrspace_arr_3))))
(assert
 (let ((?x30 (struct_type_pointer_struct (_ bv0 64) (_ bv0 64))))
 (= NULL ?x30)))
(assert
 (let ((?x31 (struct_type_pointer_struct (_ bv1 64) (_ bv0 64))))
 (= INVALID ?x31)))
(assert
 (= |nondet$symex::nondet5| |sol:@C@AssertionExample@F@_ESBMC_Main_AssertionExample#::$tmp::return_value$_nondet_bool$1?1!0&0#1|))
(assert
 (= |nondet$symex::nondet6| |sol:@C@AssertionExample@_ESBMC_Nondet_Extcall_AssertionExample#::$tmp::return_value$_nondet_bool$1?1!0&0#1|))
(assert
 (let (($x46 (bvult |sol:@C@AssertionExample@F@set@x#5&0#0| (_ bv100 256))))
(let (($x42 (and |sol:@C@AssertionExample@F@_ESBMC_Main_AssertionExample#::$tmp::return_value$_nondet_bool$1?1!0&0#1| |sol:@C@AssertionExample@_ESBMC_Nondet_Extcall_AssertionExample#::$tmp::return_value$_nondet_bool$1?1!0&0#1|)))
(let (($x47 (=> $x42 $x46)))
(let (($x48 (=> (and |execution_statet::\\guard_exec?0!0| $x42) $x46)))
(let (($x49 (=> (and true |execution_statet::\\guard_exec?0!0| $x42) $x46)))
(not $x49)))))))
(check-sat)
```

#### model:
```
(
  (define-fun |sol:@C@AssertionExample@F@set@x#5&0#0| () (_ BitVec 256)
    (_ bv100 256))
  (define-fun |sol:@C@AssertionExample@_ESBMC_Nondet_Extcall_AssertionExample#::$tmp::return_value$_nondet_bool$1?1!0&0#1| () Bool
    true)
  (define-fun |sol:@C@AssertionExample@F@_ESBMC_Main_AssertionExample#::$tmp::return_value$_nondet_bool$1?1!0&0#1| () Bool
    true)
  (define-fun |execution_statet::\\\\guard_exec?0!0| () Bool
    true)
  (define-fun |nondet$symex::nondet6| () Bool
    true)
  (define-fun |nondet$symex::nondet5| () Bool
    true)
  (define-fun INVALID () struct_type_pointer_struct
    (struct_type_pointer_struct (_ bv1 64) (_ bv0 64)))
  (define-fun NULL () struct_type_pointer_struct
    (struct_type_pointer_struct (_ bv0 64) (_ bv0 64)))
  (define-fun __ESBMC_addrspace_arr_1 () (Array (_ BitVec 64) struct_type_addr_space_type)
    ((as const (Array (_ BitVec 64) struct_type_addr_space_type))
  (struct_type_addr_space_type (_ bv0 64) (_ bv0 64))))
  (define-fun __ESBMC_addrspace_arr_3 () (Array (_ BitVec 64) struct_type_addr_space_type)
    (store ((as const (Array (_ BitVec 64) struct_type_addr_space_type))
         (struct_type_addr_space_type (_ bv0 64) (_ bv0 64)))
       (_ bv1 64)
       (struct_type_addr_space_type (_ bv1 64) (_ bv18446744073709551615 64))))
  (define-fun __ESBMC_addrspace_arr_2 () (Array (_ BitVec 64) struct_type_addr_space_type)
    ((as const (Array (_ BitVec 64) struct_type_addr_space_type))
  (struct_type_addr_space_type (_ bv0 64) (_ bv0 64))))
  (define-fun __ESBMC_ptr_addr_range_1 () struct_type_addr_space_type
    (struct_type_addr_space_type (_ bv1 64) (_ bv18446744073709551615 64)))
  (define-fun __ESBMC_ptr_addr_range_0 () struct_type_addr_space_type
    (struct_type_addr_space_type (_ bv0 64) (_ bv0 64)))
  (define-fun __ESBMC_ptr_obj_end_1 () (_ BitVec 64)
    (_ bv18446744073709551615 64))
  (define-fun __ESBMC_ptr_obj_start_1 () (_ BitVec 64)
    (_ bv1 64))
  (define-fun __ESBMC_ptr_obj_end_0 () (_ BitVec 64)
    (_ bv0 64))
  (define-fun __ESBMC_ptr_obj_start_0 () (_ BitVec 64)
    (_ bv0 64))
)
```

### MYTHRIL

#### Instância do solver:

```

(set-option :opt.timeout 25000)
(declare-fun call_value1 () (_ BitVec 256))
(declare-fun balance () (Array (_ BitVec 256) (_ BitVec 256)))
(declare-fun call_value2 () (_ BitVec 256))
(declare-fun sender_2 () (_ BitVec 256))
(declare-fun |2_calldatasize| () (_ BitVec 256))
(declare-fun |2_calldata| () (Array (_ BitVec 256) (_ BitVec 8)))
(declare-fun |1_calldatasize| () (_ BitVec 256))
(assert (or (not (bvule (select balance
                        #x000000000000000000000000affeaffeaffeaffeaffeaffeaffeaffeaffeaffe)
                call_value1))
    (= (select balance
               #x000000000000000000000000affeaffeaffeaffeaffeaffeaffeaffeaffeaffe)
       call_value1)))
(assert true)
(assert (= call_value1
   #x0000000000000000000000000000000000000000000000000000000000000000))
(assert (let ((a!1 (store (store balance
                         #x0000000000000000000000000901d12ebe1b195e5aa8748e62bd7734ae19b51f
                         (bvadd (select balance
                                        #x0000000000000000000000000901d12ebe1b195e5aa8748e62bd7734ae19b51f)
                                call_value1))
                  #x000000000000000000000000affeaffeaffeaffeaffeaffeaffeaffeaffeaffe
                  (bvadd (select balance
                                 #x000000000000000000000000affeaffeaffeaffeaffeaffeaffeaffeaffeaffe)
                         (bvmul #xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff
                                call_value1)))))
  (or (not (bvule (select a!1 sender_2) call_value2))
      (= (select a!1 sender_2) call_value2))))
(assert (or (= sender_2
       #x000000000000000000000000affeaffeaffeaffeaffeaffeaffeaffeaffeaffe)
    (= sender_2
       #x000000000000000000000000deadbeefdeadbeefdeadbeefdeadbeefdeadbeef)
    (= sender_2
       #x000000000000000000000000aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa)))
(assert (= call_value2
   #x0000000000000000000000000000000000000000000000000000000000000000))
(assert (or (not (= ((_ extract 255 3) |2_calldatasize|)
            #b0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000))
    (bvule #b100 ((_ extract 2 0) |2_calldatasize|))))
(assert (not (and (= (select |2_calldata|
                     #x0000000000000000000000000000000000000000000000000000000000000003)
             #x45)
          (not (bvsle |2_calldatasize|
                      #x0000000000000000000000000000000000000000000000000000000000000003))
          (= (select |2_calldata|
                     #x0000000000000000000000000000000000000000000000000000000000000002)
             #xf2)
          (not (bvsle |2_calldatasize|
                      #x0000000000000000000000000000000000000000000000000000000000000002))
          (= (select |2_calldata|
                     #x0000000000000000000000000000000000000000000000000000000000000001)
             #xa4)
          (not (bvsle |2_calldatasize|
                      #x0000000000000000000000000000000000000000000000000000000000000001))
          (= (select |2_calldata|
                     #x0000000000000000000000000000000000000000000000000000000000000000)
             #x3f)
          (not (bvsle |2_calldatasize|
                      #x0000000000000000000000000000000000000000000000000000000000000000)))))
(assert (and (= (select |2_calldata|
                #x0000000000000000000000000000000000000000000000000000000000000003)
        #xb1)
     (not (bvsle |2_calldatasize|
                 #x0000000000000000000000000000000000000000000000000000000000000003))
     (= (select |2_calldata|
                #x0000000000000000000000000000000000000000000000000000000000000002)
        #x47)
     (not (bvsle |2_calldatasize|
                 #x0000000000000000000000000000000000000000000000000000000000000002))
     (= (select |2_calldata|
                #x0000000000000000000000000000000000000000000000000000000000000001)
        #xfe)
     (not (bvsle |2_calldatasize|
                 #x0000000000000000000000000000000000000000000000000000000000000001))
     (= (select |2_calldata|
                #x0000000000000000000000000000000000000000000000000000000000000000)
        #x60)
     (not (bvsle |2_calldatasize|
                 #x0000000000000000000000000000000000000000000000000000000000000000))))
(assert (bvsle #x0000000000000000000000000000000000000000000000000000000000000020
       (bvadd #xfffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffc
              |2_calldatasize|)))
(assert true)
(assert (let ((a!1 (or (bvsle |2_calldatasize|
                      #x0000000000000000000000000000000000000000000000000000000000000023)
               (= ((_ extract 7 7)
                    (select |2_calldata|
                            #x0000000000000000000000000000000000000000000000000000000000000023))
                  #b0)))
      (a!3 (bvule #b1100100
                  (ite (bvsle |2_calldatasize|
                              #x0000000000000000000000000000000000000000000000000000000000000023)
                       #b0000000
                       ((_ extract 6 0)
                         (select |2_calldata|
                                 #x0000000000000000000000000000000000000000000000000000000000000023))))))
(let ((a!2 (and a!1
                (or (bvsle |2_calldatasize|
                           #x0000000000000000000000000000000000000000000000000000000000000022)
                    (= (select |2_calldata|
                               #x0000000000000000000000000000000000000000000000000000000000000022)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x0000000000000000000000000000000000000000000000000000000000000021)
                    (= (select |2_calldata|
                               #x0000000000000000000000000000000000000000000000000000000000000021)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x0000000000000000000000000000000000000000000000000000000000000020)
                    (= (select |2_calldata|
                               #x0000000000000000000000000000000000000000000000000000000000000020)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x000000000000000000000000000000000000000000000000000000000000001f)
                    (= (select |2_calldata|
                               #x000000000000000000000000000000000000000000000000000000000000001f)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x000000000000000000000000000000000000000000000000000000000000001e)
                    (= (select |2_calldata|
                               #x000000000000000000000000000000000000000000000000000000000000001e)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x000000000000000000000000000000000000000000000000000000000000001d)
                    (= (select |2_calldata|
                               #x000000000000000000000000000000000000000000000000000000000000001d)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x000000000000000000000000000000000000000000000000000000000000001c)
                    (= (select |2_calldata|
                               #x000000000000000000000000000000000000000000000000000000000000001c)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x000000000000000000000000000000000000000000000000000000000000001b)
                    (= (select |2_calldata|
                               #x000000000000000000000000000000000000000000000000000000000000001b)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x000000000000000000000000000000000000000000000000000000000000001a)
                    (= (select |2_calldata|
                               #x000000000000000000000000000000000000000000000000000000000000001a)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x0000000000000000000000000000000000000000000000000000000000000019)
                    (= (select |2_calldata|
                               #x0000000000000000000000000000000000000000000000000000000000000019)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x0000000000000000000000000000000000000000000000000000000000000018)
                    (= (select |2_calldata|
                               #x0000000000000000000000000000000000000000000000000000000000000018)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x0000000000000000000000000000000000000000000000000000000000000017)
                    (= (select |2_calldata|
                               #x0000000000000000000000000000000000000000000000000000000000000017)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x0000000000000000000000000000000000000000000000000000000000000016)
                    (= (select |2_calldata|
                               #x0000000000000000000000000000000000000000000000000000000000000016)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x0000000000000000000000000000000000000000000000000000000000000015)
                    (= (select |2_calldata|
                               #x0000000000000000000000000000000000000000000000000000000000000015)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x0000000000000000000000000000000000000000000000000000000000000014)
                    (= (select |2_calldata|
                               #x0000000000000000000000000000000000000000000000000000000000000014)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x0000000000000000000000000000000000000000000000000000000000000013)
                    (= (select |2_calldata|
                               #x0000000000000000000000000000000000000000000000000000000000000013)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x0000000000000000000000000000000000000000000000000000000000000012)
                    (= (select |2_calldata|
                               #x0000000000000000000000000000000000000000000000000000000000000012)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x0000000000000000000000000000000000000000000000000000000000000011)
                    (= (select |2_calldata|
                               #x0000000000000000000000000000000000000000000000000000000000000011)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x0000000000000000000000000000000000000000000000000000000000000010)
                    (= (select |2_calldata|
                               #x0000000000000000000000000000000000000000000000000000000000000010)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x000000000000000000000000000000000000000000000000000000000000000f)
                    (= (select |2_calldata|
                               #x000000000000000000000000000000000000000000000000000000000000000f)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x000000000000000000000000000000000000000000000000000000000000000e)
                    (= (select |2_calldata|
                               #x000000000000000000000000000000000000000000000000000000000000000e)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x000000000000000000000000000000000000000000000000000000000000000d)
                    (= (select |2_calldata|
                               #x000000000000000000000000000000000000000000000000000000000000000d)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x000000000000000000000000000000000000000000000000000000000000000c)
                    (= (select |2_calldata|
                               #x000000000000000000000000000000000000000000000000000000000000000c)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x000000000000000000000000000000000000000000000000000000000000000b)
                    (= (select |2_calldata|
                               #x000000000000000000000000000000000000000000000000000000000000000b)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x000000000000000000000000000000000000000000000000000000000000000a)
                    (= (select |2_calldata|
                               #x000000000000000000000000000000000000000000000000000000000000000a)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x0000000000000000000000000000000000000000000000000000000000000009)
                    (= (select |2_calldata|
                               #x0000000000000000000000000000000000000000000000000000000000000009)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x0000000000000000000000000000000000000000000000000000000000000008)
                    (= (select |2_calldata|
                               #x0000000000000000000000000000000000000000000000000000000000000008)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x0000000000000000000000000000000000000000000000000000000000000007)
                    (= (select |2_calldata|
                               #x0000000000000000000000000000000000000000000000000000000000000007)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x0000000000000000000000000000000000000000000000000000000000000006)
                    (= (select |2_calldata|
                               #x0000000000000000000000000000000000000000000000000000000000000006)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x0000000000000000000000000000000000000000000000000000000000000005)
                    (= (select |2_calldata|
                               #x0000000000000000000000000000000000000000000000000000000000000005)
                       #x00))
                (or (bvsle |2_calldatasize|
                           #x0000000000000000000000000000000000000000000000000000000000000004)
                    (= (select |2_calldata|
                               #x0000000000000000000000000000000000000000000000000000000000000004)
                       #x00)))))
  (or (not a!2) a!3))))
(assert (let ((a!1 (or (not (= ((_ extract 255 13) |1_calldatasize|)
                       #b000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000))
               (bvule #b1001110001000 ((_ extract 12 0) |1_calldatasize|)))))
  (or (not a!1)
      (= |1_calldatasize|
         #x0000000000000000000000000000000000000000000000000000000000001388))))
(assert (let ((a!1 (not (= ((_ extract 255 70)
                     (select balance
                             #x000000000000000000000000affeaffeaffeaffeaffeaffeaffeaffeaffeaffe))
                   #b000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000))))
(let ((a!2 (or a!1
               (bvule #b1101100011010111001001101011011100010111011110101000000000000000000000
                      ((_ extract 69 0)
                        (select balance
                                #x000000000000000000000000affeaffeaffeaffeaffeaffeaffeaffeaffeaffe))))))
  (or (not a!2)
      (= (select balance
                 #x000000000000000000000000affeaffeaffeaffeaffeaffeaffeaffeaffeaffe)
         #x00000000000000000000000000000000000000000000003635c9adc5dea00000)))))
(assert (let ((a!1 (or (not (= ((_ extract 255 13) |2_calldatasize|)
                       #b000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000))
               (bvule #b1001110001000 ((_ extract 12 0) |2_calldatasize|)))))
  (or (not a!1)
      (= |2_calldatasize|
         #x0000000000000000000000000000000000000000000000000000000000001388))))
(assert (let ((a!1 (not (= ((_ extract 255 70) (select balance sender_2))
                   #b000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000))))
(let ((a!2 (or a!1
               (bvule #b1101100011010111001001101011011100010111011110101000000000000000000000
                      ((_ extract 69 0) (select balance sender_2))))))
  (or (not a!2)
      (= (select balance sender_2)
         #x00000000000000000000000000000000000000000000003635c9adc5dea00000)))))
(assert (let ((a!1 (not (= ((_ extract 255 67)
                     (select balance
                             #x000000000000000000000000affeaffeaffeaffeaffeaffeaffeaffeaffeaffe))
                   #b000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000))))
(let ((a!2 (or a!1
               (bvule #b1010110101111000111010111100010110101100011000100000000000000000000
                      ((_ extract 66 0)
                        (select balance
                                #x000000000000000000000000affeaffeaffeaffeaffeaffeaffeaffeaffeaffe))))))
  (or (not a!2)
      (= (select balance
                 #x000000000000000000000000affeaffeaffeaffeaffeaffeaffeaffeaffeaffe)
         #x0000000000000000000000000000000000000000000000056bc75e2d63100000)))))
(assert (let ((a!1 (not (= ((_ extract 255 67)
                     (select balance
                             #x000000000000000000000000deadbeefdeadbeefdeadbeefdeadbeefdeadbeef))
                   #b000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000))))
(let ((a!2 (or a!1
               (bvule #b1010110101111000111010111100010110101100011000100000000000000000000
                      ((_ extract 66 0)
                        (select balance
                                #x000000000000000000000000deadbeefdeadbeefdeadbeefdeadbeefdeadbeef))))))
  (or (not a!2)
      (= (select balance
                 #x000000000000000000000000deadbeefdeadbeefdeadbeefdeadbeefdeadbeef)
         #x0000000000000000000000000000000000000000000000056bc75e2d63100000)))))
(assert (let ((a!1 (not (= ((_ extract 255 67)
                     (select balance
                             #x0000000000000000000000000901d12ebe1b195e5aa8748e62bd7734ae19b51f))
                   #b000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000))))
(let ((a!2 (or a!1
               (bvule #b1010110101111000111010111100010110101100011000100000000000000000000
                      ((_ extract 66 0)
                        (select balance
                                #x0000000000000000000000000901d12ebe1b195e5aa8748e62bd7734ae19b51f))))))
  (or (not a!2)
      (= (select balance
                 #x0000000000000000000000000901d12ebe1b195e5aa8748e62bd7734ae19b51f)
         #x0000000000000000000000000000000000000000000000056bc75e2d63100000)))))
(assert true)
(check-sat)
```

#### model:  

```
(
  (define-fun sender_2 () (_ BitVec 256)
    (_ bv1271270613000041655817448348132275889066893754095 256))
  (define-fun |2_calldatasize| () (_ BitVec 256)
    (_ bv4099 256))
  (define-fun |2_calldata| () (Array (_ BitVec 256) (_ BitVec 8))
    (let ((a!1 (store (store (store ((as const (Array (_ BitVec 256) (_ BitVec 8)))
                                  (_ bv254 8))
                                (_ bv3 256)
                                (_ bv177 8))
                         (_ bv0 256)
                         (_ bv96 8))
                  (_ bv2 256)
                  (_ bv71 8))))
  (store a!1 (_ bv19 256) (_ bv1 8))))
  (define-fun balance () (Array (_ BitVec 256) (_ BitVec 256))
    ((as const (Array (_ BitVec 256) (_ BitVec 256))) (_ bv0 256)))
  (define-fun |1_calldatasize| () (_ BitVec 256)
    (_ bv0 256))
  (define-fun call_value2 () (_ BitVec 256)
    (_ bv0 256))
  (define-fun call_value1 () (_ BitVec 256)
    (_ bv0 256))
)
```




