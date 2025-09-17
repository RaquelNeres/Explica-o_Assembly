# 🔧 Aprendendo Assembly - Guia Prático

Este repositório contém exercícios e exemplos práticos para aprender Assembly x86 de forma clara e didática. Cada código é comentado detalhadamente e acompanhado de comparações com C para facilitar o entendimento.

## 📚 Índice

- [O que é Assembly?](#o-que-é-assembly)
- [Por que Aprender Assembly?](#por-que-aprender-assembly)
- [Conceitos Fundamentais](#conceitos-fundamentais)
- [Macros e Procedimentos](#macros-e-procedimentos)
- [Exemplos Práticos](#exemplos-práticos)
- [Como Executar](#como-executar)
- [Estrutura do Projeto](#estrutura-do-projeto)

## 🤔 O que é Assembly?

Assembly é uma linguagem de programação de baixo nível que está muito próxima da linguagem de máquina. Cada instrução em Assembly corresponde quase diretamente a uma instrução que o processador pode executar.

### Comparação Visual: C vs Assembly

**Em C:**
```c
#include <stdio.h>

int main() {
    int angulo;
    printf("Digite o ângulo: ");
    scanf("%d", &angulo);
    
    if (angulo == 0) {
        printf("LESTE");
    } else if (angulo < 90) {
        printf("NORDESTE");
    }
    
    return 0;
}
```

**Em Assembly (baseado no problema1.asm):**
```assembly
ORG 100h

LEA BX, str_aviso           ; Carrega a string de solicitação
PRINT_S BX                  ; Macro para imprimir string

CALL SCAN_NUM               ; Procedimento para ler número
MOV angulo, CX              ; Armazena o número lido

CMP angulo, 0               ; Compara ângulo com 0
JNZ not_leste               ; Salta se não for 0
LEA BX, str_leste           ; Carrega string "LESTE"
PRINT_S BX                  ; Imprime a string
JMP end                     ; Pula para o fim

not_leste:
CMP angulo, 90              ; Compara com 90
JNL not_nordeste            ; Salta se >= 90
LEA BX, str_nordeste        ; Carrega "NORDESTE"
PRINT_S BX
JMP end

; ... continua com outras direções
```

## 💡 Por que Aprender Assembly?

1. **Entendimento Profundo**: Compreender como o computador realmente funciona
2. **Controle Total**: Acesso direto aos recursos do hardware
3. **Otimização**: Escrever código extremamente eficiente
4. **Debugging**: Facilitar a depuração de programas complexos
5. **Sistemas Embarcados**: Programação de baixo nível para microcontroladores

## 🧠 Conceitos Fundamentais

### 1. Registradores
Os registradores são pequenas áreas de armazenamento dentro do processador, extremamente rápidas.

**Registradores principais usados nos exemplos:**
- **AX/AL/AH**: Acumulador (AL = parte baixa, AH = parte alta)
- **BX**: Base, usado para endereçamento
- **CX**: Contador, usado em loops e como resultado de SCAN_NUM
- **DX**: Dados, usado em operações de divisão

### 2. Instruções de Movimentação
```assembly
MOV AX, 10          ; AX = 10
MOV BX, AX          ; BX = AX (BX = 10)
MOV CX, angulo      ; CX = conteúdo da variável angulo
LEA BX, str_aviso   ; BX = endereço da string str_aviso
```

### 3. Comparações e Saltos
**Em C:**
```c
if (a > b) {
    // código
} else {
    // outro código
}
```

**Em Assembly:**
```assembly
CMP AX, BX          ; Compara AX com BX
JG maior            ; Salta se AX > BX
; código do else
JMP fim
maior:
; código do if
fim:
```

## ⚙️ Macros e Procedimentos

### Macros - Código Reutilizável

As macros são pedaços de código que podem ser inseridos em vários lugares do programa.

**Exemplo da macro PUTC (do nosso código):**
```assembly
PUTC    MACRO   char        ; Macro com "parâmetro" char
        PUSH    AX          ; Guarda AX na stack
        MOV     AL, char    ; Coloca o char em AL
        MOV     AH, 0Eh     ; Função de impressão de caractere
        INT     10h         ; Interrupção do BIOS
        POP     AX          ; Restaura AX
ENDM

; Uso:
PUTC 'A'                    ; Imprime a letra 'A'
PUTC ' '                    ; Imprime um espaço
```

**Equivalente em C:**
```c
void putc(char character) {
    printf("%c", character);
}

// Uso:
putc('A');
putc(' ');
```

### Procedimentos - Funções em Assembly

**Exemplo do procedimento SCAN_NUM (do nosso código):**
```assembly
SCAN_NUM    PROC        ; Define o procedimento
    PUSH    DX          ; Salva registradores
    PUSH    AX          
    PUSH    SI
    
    MOV     CX, 0       ; Inicializa resultado como 0
    
next_digit:
    GETC                ; Pega um caractere
    ; ... lógica de conversão ...
    JMP     next_digit
    
stop_input:
    POP     SI          ; Restaura registradores
    POP     AX
    POP     DX
    RET                 ; Retorna (resultado em CX)
SCAN_NUM    ENDP

; Uso:
CALL SCAN_NUM           ; Chama o procedimento
MOV numero, CX          ; CX contém o número lido
```

**Equivalente em C:**
```c
int scan_num() {
    int numero = 0;
    char c;
    
    while ((c = getchar()) != '\n') {
        if (c >= '0' && c <= '9') {
            numero = numero * 10 + (c - '0');
        }
    }
    
    return numero;
}

// Uso:
int numero = scan_num();
```

## 🔍 Exemplos Práticos

### Exemplo 1: Classificador de Direções (problema1.asm)

**Objetivo**: Ler um ângulo e determinar a direção correspondente.

**Em C:**
```c
#include <stdio.h>

int main() {
    int angulo;
    printf("Digite o valor do ângulo: ");
    scanf("%d", &angulo);
    
    printf("O ângulo %d representa a direção ", angulo);
    
    if (angulo == 0) {
        printf("LESTE");
    } else if (angulo < 90) {
        printf("NORDESTE");
    } else if (angulo == 90) {
        printf("NORTE");
    } else if (angulo < 180) {
        printf("NOROESTE");
    } else if (angulo == 180) {
        printf("OESTE");
    } else if (angulo < 270) {
        printf("SUDOESTE");
    } else if (angulo == 270) {
        printf("SUL");
    } else if (angulo < 360) {
        printf("SUDESTE");
    } else {
        printf("ÂNGULO INVÁLIDO");
    }
    
    return 0;
}
```

**Em Assembly (estrutura do nosso código):**
```assembly
; Solicitação de entrada
LEA BX, str_aviso           ; "Digite o valor do ângulo..."
PRINT_S BX
CALL SCAN_NUM               ; Lê o número
MOV angulo, CX              ; Armazena o ângulo

; Impressão inicial
LEA BX, str_inicio          ; "O ângulo"
PRINT_S BX
MOV AX, angulo
CALL PRINT_NUM              ; Imprime o número
LEA BX, str_representa      ; "representa a direção"
PRINT_S BX

; Lógica de decisão
CMP angulo, 0
JNZ not_leste
LEA BX, str_leste
PRINT_S BX
JMP end

not_leste:
CMP angulo, 90
JNL not_nordeste
LEA BX, str_nordeste
PRINT_S BX
JMP end

; ... continua para outras direções ...
```

### Exemplo 2: Sequência de Fibonacci (problema2.asm)

**Objetivo**: Gerar números de Fibonacci em um intervalo [m, n].

**Em C:**
```c
#include <stdio.h>

int main() {
    int m, n, a = 1, b = 1, c, cont = 0;
    
    do {
        printf("Primeiro elemento (M): ");
        scanf("%d", &m);
        printf("Último elemento (N): ");
        scanf("%d", &n);
    } while (n <= m || m < 3);
    
    while (1) {
        c = a + b;
        if (c >= m) {
            printf("%d ", c);
            cont++;
        }
        if (c >= n) break;
        a = b;
        b = c;
    }
    
    printf("Total de números impressos: %d", cont);
    return 0;
}
```

**Em Assembly (estrutura do nosso código):**
```assembly
while1:                     ; Loop de validação de entrada
    LEA AX, str_m
    PRINT_S AX
    CALL SCAN_NUM
    MOV m, CX               ; Armazena M
    
    LEA AX, str_n
    PRINT_S AX
    CALL SCAN_NUM
    MOV n, CX               ; Armazena N
    
    CMP CX, m               ; Compara n com m
    JG comparou             ; Se n > m, continua
    JMP while1              ; Senão, repete entrada

while2:                     ; Loop principal de Fibonacci
    MOV AX, b
    ADD AX, a
    MOV c, AX               ; c = a + b
    
    CMP AX, m
    JL incremento           ; Se c < m, não imprime
    CALL PRINT_NUM          ; Imprime c
    INC cont                ; Incrementa contador
    
incremento:
    MOV BX, b
    MOV a, BX               ; a = b
    MOV BX, c
    MOV b, BX               ; b = c
    
    MOV AX, n
    CMP c, AX
    JL while2               ; Se c < n, continua
```

### Exemplo 3: Cálculo de MDC (problema3.asm)

**Algoritmo de Euclides em Assembly:**

**Em C:**
```c
int mdc(int a, int b) {
    while (b != 0) {
        int temp = a % b;
        a = b;
        b = temp;
    }
    return a;
}
```

**Em Assembly (do nosso código):**
```assembly
CALCULAR_MDC    PROC       
    ; BX = num1, CX = num2
while:
    CMP CX, 0               ; Compara B com 0
    JE fim                  ; Se B = 0, termina
    
    MOV DX, 0               ; Limpa DX para divisão
    MOV AX, BX              ; AX = A
    DIV CX                  ; DX = AX % CX
    MOV BX, CX              ; A = B
    MOV CX, DX              ; B = A % B
    
    JMP while
    
fim:
    RET                     ; Retorna resultado em BX
CALCULAR_MDC    ENDP
```

## 🚀 Como Executar

### 1. Instalar o EMU8086
```
1. Baixe o EMU8086 (emulador usado nos exemplos)
2. Instale seguindo as instruções
3. Abra os arquivos .asm no EMU8086
```

### 2. Compilar e Executar
```
1. Abra o arquivo no EMU8086
2. Pressione F9 para compilar
3. Pressione F10 para executar passo a passo
4. Ou pressione F5 para executar diretamente
```

### 3. Para NASM (alternativa)
```bash
nasm -f bin programa.asm -o programa.com
programa.com
```

## 📁 Estrutura do Projeto

```
trabalhoaoc/
├── problema1.asm              # Classificador de direções por ângulo
├── problema1.artesanal.asm    # Versão alternativa sem macros prontas
├── problema2.asm              # Gerador de sequência Fibonacci
├── problema3.asm              # Calculadora de MDC (Euclides)
├── scanum.asm                 # Macro SCAN_NUM reutilizável
└── README.md                  # Este arquivo
```

## 🔧 Técnicas Utilizadas nos Códigos

### 1. Interrupções do Sistema
```assembly
INT 10h                     ; Interrupções de vídeo (BIOS)
INT 16h                     ; Interrupções de teclado
INT 21h                     ; Interrupções DOS
```

### 2. Manipulação de Strings
```assembly
; Definição de strings
str_aviso DB 'Digite o valor: $'

; Impressão usando interrupção DOS
MOV DX, str_aviso
MOV AH, 9
INT 21h
```

### 3. Conversão ASCII para Inteiro
```assembly
; Caractere '5' (ASCII 53) vira número 5
SUB AL, '0'                 ; ou SUB AL, 30h
; AL agora contém 5
```

### 4. Controle de Fluxo
```assembly
; Saltos condicionais baseados em comparações
CMP AX, BX                  ; Compara AX com BX
JE  igual                   ; Salta se igual
JNE diferente               ; Salta se diferente
JG  maior                   ; Salta se AX > BX
JL  menor                   ; Salta se AX < BX
```

## 📝 Dicas de Estudo

1. **Leia os Comentários**: Cada linha importante está comentada
2. **Trace a Execução**: Use F10 no EMU8086 para executar passo a passo
3. **Entenda os Registradores**: Observe como AX, BX, CX, DX são usados
4. **Compare com C**: Sempre pense como seria o código equivalente em C
5. **Pratique as Macros**: Entenda como PUTC, GETC, PRINT_S funcionam

## 🛠️ Ferramentas Úteis

- **EMU8086**: Emulador e debugger usado nos exemplos
- **Debug**: Ferramenta de debug do DOS
- **NASM**: Assembler alternativo
- **DOSBox**: Para executar programas DOS em sistemas modernos

## 📚 Conceitos Importantes

### Pilha (Stack)
```assembly
PUSH AX                     ; Empilha AX
PUSH BX                     ; Empilha BX
; ... código ...
POP BX                      ; Desempilha em BX (LIFO - Last In, First Out)
POP AX                      ; Desempilha em AX
```

### Endereçamento
```assembly
MOV AX, numero              ; AX = conteúdo de 'numero'
LEA BX, numero              ; BX = endereço de 'numero'
MOV CX, [BX]                ; CX = conteúdo do endereço em BX
```

### Flags e Comparações
```assembly
CMP AX, 0                   ; Compara AX com 0, atualiza flags
JZ  zero                    ; Salta se Zero Flag estiver setada
JNZ nao_zero                ; Salta se Zero Flag não estiver setada
```

---

*"Assembly é onde o software encontra o hardware - cada instrução conta!"*

**Dica Final**: Execute os códigos passo a passo no EMU8086 para ver exatamente como cada instrução modifica os registradores e a memória. É a melhor forma de entender Assembly!
