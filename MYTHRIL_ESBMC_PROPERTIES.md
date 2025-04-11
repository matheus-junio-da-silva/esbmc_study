Print da instância do solver no momento antes da detecção (check) da vulnerabilidade:  

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
(declare-fun |sol:@C@DivisionByZero@F@_ESBMC_Main_DivisionByZero#::$tmp::return_value$_nondet_bool$1?1!0&0#1| () Bool)
(declare-fun |nondet$symex::nondet5| () Bool)
(declare-fun |sol:@C@DivisionByZero@_ESBMC_Nondet_Extcall_DivisionByZero#::$tmp::return_value$_nondet_bool$1?1!0&0#1| () Bool)
(declare-fun |nondet$symex::nondet6| () Bool)
(declare-fun |sol:@C@DivisionByZero@F@divide@b#7&0#0| () (_ BitVec 256))
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
 (= |nondet$symex::nondet5| |sol:@C@DivisionByZero@F@_ESBMC_Main_DivisionByZero#::$tmp::return_value$_nondet_bool$1?1!0&0#1|))
(assert
 (= |nondet$symex::nondet6| |sol:@C@DivisionByZero@_ESBMC_Nondet_Extcall_DivisionByZero#::$tmp::return_value$_nondet_bool$1?1!0&0#1|))
(assert
 (let (($x46 (= |sol:@C@DivisionByZero@F@divide@b#7&0#0| (_ bv0 256))))
(let (($x47 (not $x46)))
(let (($x42 (and |sol:@C@DivisionByZero@F@_ESBMC_Main_DivisionByZero#::$tmp::return_value$_nondet_bool$1?1!0&0#1| |sol:@C@DivisionByZero@_ESBMC_Nondet_Extcall_DivisionByZero#::$tmp::return_value$_nondet_bool$1?1!0&0#1|)))
(let (($x48 (=> $x42 $x47)))
(let (($x49 (=> (and |execution_statet::\\guard_exec?0!0| $x42) $x47)))
(let (($x50 (=> (and true |execution_statet::\\guard_exec?0!0| $x42) $x47)))
(not $x50))))))))
(check-sat)
```
