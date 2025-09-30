> Operační systémy 2025/26
# 1. přednáška
## výsledky kvizíku
1. add `C` to value in `X`
ano
2. copy value from `X` to `Y`
ano
3. read value from `M` and store it in `Y`
ano
4. compute sin of value in `X` and store it in `Y`
Překvapivě ano, může být. Některé procesory to mají v instrukční sadě (Intel), nicméně dnes se často tyhle velké instrukce stejně provádějí po mikrooperacích
5. compute sum of values in `X` and `Y` and store it in `M`
Ne nutně, občas neumíme zakódovat instrukce, které vypadají jednoduše
6. read value from `M` and if it is equal to value in `X` update it to value from `Y`
ano
7. compute address as sum of `M` and `X` and `Y * 4` and read value from that address and store it in `X`
ano
8. read value from file `F` position `C` and store it in `X`
ne
> the problems are kinda simple, but the environment is complex
9. write value from register `X` and write it to memory variable `V`
ne, procesor pracuje s adesami
10. copy memory block from adress `X` of size `Y` to adress `Z`
záleží, spíš ne

## můžu vidět instrukce?
ano :¨)
```
gcc -o main main.c
objdump -d main

nebo

clang -o main main.c
objdump -d main
```

## user mode vs. privileged mode
- když program chce dělat něco, co může interferovat s jinými programy, je to privileged