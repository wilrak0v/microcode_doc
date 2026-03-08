# Opcode
Les OpCodes et instructions dans Micro Code sont regroupés par types en fonction de ce qu'ils font.
> Les OpCodes suivis d'un `x` ne sont pas encore implémenté.

## 1. Opérations arithmétiques
Les opérations arithmétiques concernent tout ce qui touche aux calculs. Elles s'étendent de 0x01 à 0x05.
| Instruction | OpCode | Signification                                    |
|-------------|--------|--------------------------------------------------|
| ADD         | 0x01   | Additionne les deux dernières valeurs de la pile |
| SUB         | 0x02   | Soustrais les deux dernières valeurs de la pile  |
| MUL         | 0x03   | Multiplie les deux dernières valeurs de la pile  |
| DIV         | OxO4   | Divise les deux dernières valeurs de la piles    |
| MOD    X    | 0x05   | Fait un modulo sur les deux dernières de la pile |

## 2. Opérations sur la pile
La pile est un élément central du Micro Code. C'est avec elle qu'on fait la plupart des opérations.
| Instruction | OpCode | Signification                        |
|-------------|--------|--------------------------------------|
| PUSH VAL    | 0x06   | Ajoute `VAL` sur la pile             |
| DROP        | 0x07   | Retire la dernière valeur de la pile |
| SWAP        | 0x08   | Swap les deux dernières valeurs      |
| OVER        | 0x09   | Push l'avant-dernière valeur         |
| DUP         | 0x0A   | Duplique la dernière valeur          |

**Exemple**
```Text
PUSH 10                ; -> 10
PUSH 20                ; -> 10 : 20     
SWAP                   ; -> 20 : 10
OVER                   ; -> 20 : 10 : 20
DROP                   ; -> 20 : 10
DUP                    ; -> 20 : 10 : 10
```
En hexadécimal : `0x06 0x0A 0x00 0x00 0x00 0x06 0x14 0x00 0x00 0x00 0x08 0x09 0x07 0x0A`.

## 3. Opérations logiques et sauts
Les opérations logiques et les sauts permettent de créer des comportements similaires aux `if` dans les langages habituels.
| Instruction  | OpCode | Signification                  |
|--------------|--------|--------------------------------|
| JMP          | 0x0B   | Saute à l'adresse sur la pile  |
| JZ           | 0x0C   | Saute à addr si stack[sp] == 0 |
| JNZ          | 0x0D   | Saute à addr si stack[sp] != 0 |
| EQ           | 0x0E   | Push 1 si a == b, sinon 0      |
| LT           | 0x0F   | Push 1 si a < b, sinon 0       |
| GT           | 0x10   | Push 1 si a > b, sinon 0       |

## 4. Opérations sur la mémoire 
Les opérations sur la mémoire permettent d'accéder à une mémoire RAM dans la plage définit par le header.
| Instruction  | OpCode | Signification                 |
|--------------|--------|-------------------------------|
| STORE  ADDR  | 0x11   | Store stack[sp] à ADDR        |
| LOAD   ADDR  | 0x12   | Load ADDR et push sur la pile |

## 5. Opérations spéciales / Systèmes
Ce sont des opérations qui dépendent du système qui exécute le Micro Code. Par exemple, il se peut que certaines instructions comme les instructions graphiques ne soient pas disponible sur des microcontrôleurs modestes.
| Instruction  | Opcode | Signification                   |
|--------------|--------|---------------------------------|
| HALT         | 0x00   | Stop le programme               |
| DELAY        | 0x13   | Fais un delay de stack[sp] ms   |
| PRINT        | 0x14   | Affiche la valeur de la pile    |
| PUTC         | 0x15   | Affiche le caractère de la pile |
