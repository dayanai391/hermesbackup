# C++ Curriculum & Exercise Bank

Target pace: one lesson per exchange. Each entry = concept, example seed, exercise.

1. **Hello & I/O** — `#include <iostream>`, `main`, `cout << ... << endl`. Exercise: print name inside a sentence.
2. **Variables & types** — `int`, `double`, `char`, `bool`, `string`; `=` is assignment; every statement ends `;`. Exercise: price × quantity total.
3. **Input & operators** — `cin >>`, `+ - * / %`; integer division trap (`10/3 == 3`), `10.0/3` fixes it; `a % b` parity. Exercise: discount calculator (price, qty, %).
4. **Conditionals** — `if / else if / else`, `==` vs `=`, `&&`, `||`, `!`. Exercise: grade printer + reject out-of-range input (`<0 || >20` → `Adad Ghalat!`).
5. **Loops** — `for` anatomy (start; condition; step), `while`, off-by-one (`<` vs `<=`), infinite-loop warning + Ctrl+C. Exercise: sum 1..n, multiplication table.
6. **Arrays** *(NEXT)* — declaration `int a[5] = {...}`, zero-based index, size fixed, traverse with `for`, out-of-bounds = undefined garbage. Exercise: 5 scores → max + count of passes (≥15).
7. **Functions** — `returnType name(params)`, prototype vs definition, pass-by-value; why it beats copy-paste. Exercise: `int max3(int,int,int)`.
8. **Strings** — `string` ops, `length()`, char loop, `getline` vs `cin >>`. Exercise: count vowels.
9. **switch & char** — `switch/case/default/break`; calculator with `switch(op)`. Exercise: rebuild the calculator from lesson 4.
10. **Structs** — `struct Student { string name; double score; };`, dot access, arrays of structs. Exercise: classroom top-3.
11. **File I/O** — `ifstream`/`ofstream`, read scores from file, write report. Exercise: persist the classroom.
12. **References & pointers lite** — `&`, `*`, why swap(a,b) needs them. Stop before manual memory; she's a beginner.

Pacing notes: if she struggles on an exercise, drop a hint (not the solution) and
wait; if she aces it, the next lesson can open with a slightly harder variant.
