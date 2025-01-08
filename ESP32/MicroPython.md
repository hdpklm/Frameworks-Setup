# Micropython With ESP32-S3

## download idf, and micropython
```console
mkdir /app
cd /app
git clone -b release/v5.2 https://github.com/espressif/esp-idf.git
git clone -b v1.24-release https://github.com/micropython/micropython.git
```

## Build image for ESP32-S3
```console
cd /app/micropython
/app/esp-idf/install.sh
. /app/esp-idf/export.sh
make -C mpy-cross
make -C ports/esp32 BOARD=ESP32_GENERIC_S3
```

## Add python module
- put your python module file in `/app/micropython/ports/esp32/modules` ex: `my_module.py`
- also can add sub-directory like `/app/micropython/ports/esp32/modules/my_modules/my_funcs.py`
- example:
```python
import my_module
import my_modules.my_funcs
```


## Add c module
- create a file in `micropython/ports/esp32/[module-name].c`
- add [file name] to compile list: `micropython/ports/esp32/esp32_common.cmake` add to `MICROPY_SOURCE_PORT`
- code:
```c
#include "py/obj.h"
#include "py/runtime.h"

// repmlace "[mathlib]" with module name
// repmlace "[factorial]" with funcion name
static mp_int_t factorial_helper(mp_int_t x) {
    if (x < 1) {
        return 1;
    }
    return x * factorial_helper(x - 1);
}

static mp_obj_t [factorial](mp_obj_t x_obj) {
    mp_int_t x = mp_obj_get_int(x_obj);
    mp_int_t result = factorial_helper(x);
    return mp_obj_new_int(result);
}
static MP_DEFINE_CONST_FUN_OBJ_1([factorial]_obj, [factorial]);

static const mp_rom_map_elem_t [mathlib]_globals_table[] = {
    { MP_ROM_QSTR(MP_QSTR_[factorial]), MP_ROM_PTR(&[factorial]_obj) },
};
static MP_DEFINE_CONST_DICT([mathlib]_globals, [mathlib]_globals_table);

const mp_obj_module_t [mathlib] = {
    .base = { &mp_type_module },
    .globals = (mp_obj_dict_t *)&[mathlib]_globals,
};

MP_REGISTER_MODULE(MP_QSTR_[mathlib], [mathlib]);
```
- example:
```python
import mathlib
mathlib.factorial(5)   # shuld return 120
```
