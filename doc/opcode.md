# Opcode
Les OpCodes et instructions dans Micro Code sont regroupés par types en fonction de ce qu'ils font.
> Les OpCodes suivis d'un `x` ne sont pas encore implémenté.

## 1. Opérations arithmétiques
Les opérations arithmétiques concernent tout ce qui touche aux calculs. Elles s'étendent de 0x01 à 0x05.

| Instruction | OpCode | Signification                                            |
| ----------- | ------ | -------------------------------------------------------- |
| ADD         | 0x01   | Additionne les deux dernières valeurs de la pile         |
| SUB         | 0x02   | Soustrais les deux dernières valeurs de la pile          |
| MUL         | 0x03   | Multiplie les deux dernières valeurs de la pile          |
| DIV         | 0x04   | Divise les deux dernières valeurs de la pile             |
| MOD         | 0x05   | Fait un modulo sur les deux dernières valeurs de la pile |
| INC         | 0x06   | Incrémente la dernière valeur de la pile                 |
| DEC         | 0x07   | Décrémente la dernière valeur de la pile                 |

## 2. Opérations sur la pile
La pile est un élément central du Micro Code. C'est avec elle qu'on fait la plupart des opérations.

| Instruction | OpCode | Signification                        |
| ----------- | ------ | ------------------------------------ |
| PUSH VAL    | 0x08   | Ajoute `VAL` sur la pile             |
| DROP        | 0X09   | Retire la dernière valeur de la pile |
| SWAP        | 0x0A   | Swap les deux dernières valeurs      |
| OVER        | 0x0B   | Push l'avant-dernière valeur         |
| DUP         | 0x0C   | Duplique la dernière valeur          |

**Exemple**
```Text
PUSH 10                ; -> 10
PUSH 20                ; -> 10 : 20     
SWAP                   ; -> 20 : 10
OVER                   ; -> 20 : 10 : 20
DROP                   ; -> 20 : 10
DUP                    ; -> 20 : 10 : 10
```
En hexadécimal : `0x08 0x0A 0x00 0x00 0x00 0x08 0x14 0x00 0x00 0x00 0x0A 0x0B 0x09 0x0C`.

## 3. Opérations logiques et sauts
Les opérations logiques et les sauts permettent de créer des comportements similaires aux `if` dans les langages habituels.

| Instruction | OpCode |  Signification                   |
| ----------- | ------ | -------------------------------- |
| JMP addr    | 0x0D   | Saute à `addr`                   |
| JZ    addr  | 0x0E   | Saute à `addr` si stack[sp] == 0 |
| JNZ addr    | 0x0F   | Saute à `addr` si stack[sp] != 0 |
| EQ          | 0x10   | Push 1 si a == b, sinon 0        |
| LT          | 0x11   | Push 1 si a < b, sinon 0         |
| GT          | 0x12   | Push 1 si a > b, sinon 0         |

## 4. Opérations sur la mémoire 
Les opérations sur la mémoire permettent d'accéder à une mémoire RAM dans la plage définit par le header.

| Instruction | OpCode |  Signification                  |
| ----------- | ------ | ------------------------------- |
| STORE addr  | 0x13   | Store stack[sp] à `addr`        |
| LOAD addr   | 0x14   | Load `addr` et push sur la pile |

## 5. Opérations spéciales / Systèmes
Ce sont des opérations qui dépendent du système qui exécute le Micro Code. Par exemple, il se peut que certaines instructions comme les instructions graphiques ne soient pas disponible sur des microcontrôleurs modestes.

| Instruction | OpCode |  Signification                  |
| ----------- | ------ | ------------------------------- |
| HALY        | 0x00   | Stop le programme               |
| DELAY       | 0x15   | Fais un delay de stack[sp] ms   |
| PRINT       | 0x16   | Affiche la valeur de la pile    |
| PUTC        | 0x17   | Affiche le caractère de le pile |

# 6. Bibliothèques et fonctions
Les **bibliothèques** et les fonctions sont la base d'un code modulaire. Les bibliothèques sont simplement des morceaux de code tiers que le code principal peut appeler afin de faciliter la tâche de ce dernier mais aussi d'éviter de réécrire la roue à chaque fois.
Ainsi si l'on souhaite calculer la racine carré d'un nombre `a`, il est bien plus intéressant d'écrire la fonction qui le fera dans une **bibliothèque** et d'appeler cette fonction lorsqu'on en a besoin au lieu d'écrire le code pour le faire à chaque fois.

| Instruction | OpCode |
| ----------- | ------ |
| INCLUDE     | 0x18   |
| FN          | 0x19   |
| RET         | 0x1A   |
| CALL        | 0x1B   |

Regardons de plus près les différentes instructions disponibles.
- `INCLUDE` permet d'inclure une bibliothèque dans le code, de l'importer, il prend en paramètre une suite de caractère qui correspond au nom de la bibliothèque et celle-ci doit se finir par `0x00`.
- `FN` définit une fonction, il prend en paramètre un `hash` correspondant au vrai nom de la fonction
- `RET` signifie 'return', il marque la fin d'une fonction et prend en paramètre le registre qui contient l'adresse vers laquelle retourner.
- `CALL` fait appel à une fonction, il prend le `hash` de la fonction en premier paramètre et le registre dans lequel stocké l'adresse courante en second paramètre.

Ainsi une déclaration de fonction ressemblerait à ce qui suit.
```Text
FN 0x4A2B1C3D
	PUSH 63
	PUTC
	RET R3
	
CALL 0x4A2B1C3D, R3
```

# 7. Opérations sur les registres 
Les  **registres** sont des petits endroits mémoires facilement accessible qui permettent de faire quelques opérations dessus. Il existe 6 registres en tout :
- 4 registres généraux qui peuvent servir à tout ce qu'on veut (R0 à R3)
- SP, le **s**tack **p**ointer est lui aussi un registre, c'est R4
- PC, le **p**rogram **counter** est lui aussi considéré comme un registre du point de vue de l'exécution du code, c'est R5

| Instruction     | OpCode | Signification                            |
| --------------- | ------ | ---------------------------------------- |
| MOVV reg val    | 0x1C   | Met `val` dans `reg`                     |
| MOVR reg1 reg2  | 0x1D   | Met `reg2` dans `reg1`                   |
| MOVS reg        | 0x1E   | Met stack[sp] dans `reg`                 |
| STORER reg addr | 0x1F   | Store `reg` à `addr`                     |
| LOADR reg addr  | 0x20   | Load `addr` et met dans `reg`            |
| ADDR reg val    | 0x21   | Add `reg` et `val` et stocke dans `reg`  |
| SUBR reg val    | 0x22   | Sub `reg` et `val` et stocke dans `reg`  |
| MULR reg val    | 0x23   | Mul `reg` par `val` et stocke dans `reg` |
| DIVR reg val    | 0x24   | Div `reg` par `val` et stocke dans `reg` |
| INCR reg        | 0x25   | Incrémente `reg`                         |
| DECR reg        | 0x26   | Décrémente `reg`                         |

