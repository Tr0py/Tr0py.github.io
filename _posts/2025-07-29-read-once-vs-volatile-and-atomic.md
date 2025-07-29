---
title: '`READ_ONCE` and `WRITE_ONCE` VS `volatile` and `atomic`'
tags:
  - phd
  - notes
---

## TL;DR Comparison Table
| Feature                | No Prefix (Normal Access) | `volatile`               | `READ_ONCE`/`WRITE_ONCE` | `atomic`                 |
|------------------------|---------------------------|--------------------------|--------------------------|--------------------------|
| Guaranteed Access      | None (can be optimized by compiler) | Yes, every access                      | Yes,specified access only                      | Yes                      |
| Atomicity              | None                      | None                     | CPU native word support  | Strong, with CPU instructions |
## 1. What do they do
`READ_ONCE` and `WRITE_ONCE` are designed to:
1. **Guarantee Memory Access**:
   - Ensure that the memory access (read or write) is not optimized away by the compiler. This is essential in concurrent programming, where the value of a variable can change unexpectedly due to operations performed by other threads or CPUs.
2. **Guarantee Atomicity**:
   - Ensure that the memory access is atomic when dealing with native word sizes. Atomicity means that the access is indivisible, preventing partial reads or writes that could lead to data corruption.
## 2. How is it achieved
The implementation of `READ_ONCE` and `WRITE_ONCE` ensures these guarantees through the following mechanisms:
```c
/*
 * Yes, this permits 64-bit accesses on 32-bit architectures. These will
 * actually be atomic in some cases (namely Armv7 + LPAE), but for others we
 * rely on the access being split into 2x32-bit accesses for a 32-bit quantity
 * (e.g. a virtual address) and a strong prevailing wind.
 */
#define compiletime_assert_rwonce_type(t)					\
	compiletime_assert(__native_word(t) || sizeof(t) == sizeof(long long),	\
		"Unsupported access size for {READ,WRITE}_ONCE().")
/*
 * Use __READ_ONCE() instead of READ_ONCE() if you do not require any
 * atomicity. Note that this may result in tears!
 */
#ifndef __READ_ONCE
#define __READ_ONCE(x) (*(const volatile __unqual_scalar_typeof(x) *)&(x))
#endif
#define READ_ONCE(x)                            \
({                                              \
    compiletime_assert_rwonce_type(x);          \
    __READ_ONCE(x);                             \
})
#define __WRITE_ONCE(x, val)                    \
do {                                            \
    *(volatile typeof(x) *)&(x) = (val);        \
} while (0)
#define WRITE_ONCE(x, val)                      \
do {                                            \
    compiletime_assert_rwonce_type(x);          \
    __WRITE_ONCE(x, val);                       \
} while (0)
```
- **Memory Access Guarantee**:
  - This is achieved by using the `volatile` keyword in the implementation (`*(volatile typeof(x) *)&(x)`), which prevents the compiler from optimizing away the read or write operation.
- **Atomicity Guarantee**:
  - Atomicity is ensured by relying on the CPU's ability to handle native word sizes atomically. For example, a 32-bit read or write on a 32-bit architecture is atomic. The macro `compiletime_assert_rwonce_type` checks that the type being accessed is either a native word size or a 64-bit type in cases where 64-bit accesses can be atomic (as specified by the architecture).
## 3. Comparison to `volatile` and `atomic`
- **`volatile`**:
  - Requires all accesses to a variable to be volatile, which can lead to performance issues due to the lack of compiler optimizations for all accesses.
  - `READ_ONCE` and `WRITE_ONCE` provide more efficient handling by making only specific reads or writes volatile, allowing other accesses to be optimized normally.
- **`atomic`**:
  - Atomic operations (e.g., `atomic_set`, `atomic_read`) often use special CPU instructions to ensure atomicity and may provide additional memory ordering guarantees.
  - `READ_ONCE` and `WRITE_ONCE` rely on the CPU's ability to perform atomic loads and stores for native word sizes without additional locking mechanisms.
  - Atomic operations can be more powerful and flexible for ensuring synchronization in complex scenarios.
## Reference
- [Memory Barriers](https://docs.kernel.org/core-api/wrappers/memory-barriers.html)
- [Linux source code - READ_ONCE - include/asm-generic/rwonce.h](https://elixir.bootlin.com/linux/v6.16/source/include/asm-generic/rwonce.h#L47)