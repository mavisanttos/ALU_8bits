# ALU 8 bits

&emsp; Nesta documentação está presente o desenvolvimento de uma ALU (Unidade Lógica e Aritmética), componente responsável por realizar operações matemáticas e manipulação de dados através de portas lógicas em números binários. Abaixo, documenta-se uma ALU de 8 bits, o que significa que é uma unidade que processa números binários de 0 até 255 (ou -128 a 127), uma vez que são as possibilidades de representação em um dado de 8 bits.

&emsp; Para a leitura dos resultados, utiliza-se a representação hexadecimal (base 16) nas saídas. Diferentemente do sistema decimal (0 a 9), o hexadecimal utiliza letras para representar valores de 10 a 15, permitindo a exibição de grupos de 4 bits em um único dígito.

&emsp; Ao operar com 8 bits, o valor máximo representado é FF (255 em decimal = 11111111). Segue abaixo uma tabela de exemplos

| Binário (8 bits) | Hexadecimal | Decimal | Observação |
| :---: | :---: | :---: | :---: |
| 0000 1010 | 0A | 10 | Valor básico |
| 0000 1111 | 0F | 15 | Limite do primeiro dígito |
| 0001 0000 | 10 | 16 | Início do segundo dígito |
| 0111 1111 | 7F | 127 | Maior número positivo (Caso considere haver números negativos na representação) |
| 1000 0000 | 80 | 128 | Início dos negativos () |
| 1111 1111 | FF | 255 | Valor máximo da ALU |

&emsp; Com isso, foram desenvolvidas as operações abaixo utilizando o simulador "Digital":

## 1. Somador

&emsp; Para processar uma soma de 8 bits, o circuito deve ser capaz de lidar com os dígitos atuais e com o "vai-um" gerado pelas colunas anteriores. Em termos mais simples, ao somar 16 + 9 nós aprendemos que somamos primeiro a unidade, com isso 6 + 9 = 15, mas não podemos deixar 15 na casa das unidades, com isso deixamos o 5 e adicionamos 10 na soma das dezenas, ou, no popular, "vai-um" para a próxima soma.

&emsp; No sistema binário, a lógica é idêntica, mas com apenas dois algarismos disponíveis (0 e 1). Assim, as regras básicas de soma são:
- 0 + 0 = 0
- 0 + 1 = 1
- 1 + 1 = 0 (e "vai-um" para a próxima coluna)
- 1 + 1 + 1 = 1 (e "vai-um" para a próxima coluna)

&emsp; No contexto utilizado, esses valores recebem rótulos, Chamamos de 'A' e 'B' os dois bits que desejamos somar, o "veio-um" da coluna anterior é chamado de 'Cin' (Carry In, ou somente Ci), o resultado da soma é o S (Sum) e o "vai-um' que passamos para a próxima etapa é denominado 'Cout' (Carry Out, ou somente Co).

### 1.1 Somador de 1 bit (Full Adder)

&emsp; Para a construção de um somador de 8 bits, primeiro foi necessário desenvolver um somador de 1 bit. Nele, inputamos três entradas, os dois bits a serem somados ('A' e 'B') e o bit de transporte de entrada ('Cin' ou 'Ci'). E esperamos a sapida 'S' e 'Co' correspondentes, conforme vemos na tabela verdade a seguir.

#### 1.1.1 Tabela Verdade do Somador de 1 bit

&emsp; A tabela verdade é uma ferramenta matemática utilizada em lógica para determinar o comportamento de um circuito digital. No caso do somador, ela define como as saídas devem reagir a cada combinação de sinais de entrada.

| A | B | Cin | S | Cout | 
| :---: | :---: | :---: | :---: | :---: |
| 0 | 0 | 0 | 0 | 0 |
| 0 | 0 | 1 | 1 | 0 |
| 0 | 1 | 0 | 1 | 0 |
| 0 | 1 | 1 | 0 | 1 |
| 1 | 0 | 0 | 1 | 0 |
| 1 | 0 | 1 | 0 | 1 |
| 1 | 1 | 0 | 0 | 1 |
| 1 | 1 | 1 | 1 | 1 |

&emsp; A tabela acima mapeia todas as 8 combinações possíveis para as entradas 'A', 'B' e 'Cin'. Para cada combinação, define-se o resultado aritmético correto para as saídas 'S' e 'Cout'.

&emsp; Através dessa tabela, podemos extrair as funções lógicas necessárias para a construção do circuito, utilizando métodos de simplificação para chegar às portas lógicas que compõem o somador.

#### 1.1.2 Mapa de Karnaugh e Expressões Lógicas 

&emsp; O Mapa de Karnaugh é um método utilizado para simplificar expressões algébricas booleanas. Ele organiza os resultados da tabela verdade em uma grade com o objetivo de identificar padrões e agrupar os níveis lógicos "1". O objetivo dele é encontrar a função lógica mais simples para a construção de um circuito de portas lógicas que tenha como saída a tabela verdade na qual estamos trabalhando.

&emsp; Aplicando o Mapa de Karnaugh na tabela acima, temos a seguinte disposição para a saída 'Cout', onde os valores de AB são representados na primeira linha e os de Cin na primeira coluna:

<div align="center">
<img src="assets\mapa_karnaugh.jpeg" alt="Mapa de Karnaugh" width="300">
</div>

&emsp; Essa disposição permite a formação de três grupos distintos, cada grupo representa uma expressão para a construção da saída 'Cout'. Considerando as cores da imagem, no grupo laranja percebemos que o sinal lógico de 'A' não afeta a saída, com isso temos a expressão $B \cdot C_{in}$ para representar esse grupo. No grupo rosa, temos que o sinal lógico de 'Cin' não afeta a saída, com isso temos que a expressão $A \cdot B$ representa esse grupo, por fim, no grupo azul, temos que o sinal lógico de 'B' não afeta a saída, com isso temos a última expressão necessária, $A \cdot C_{in}$. Como cada expressão representa uma parte da lógica, podemos chegar na expressão completa somando as três expressões, temso assim que:

$$C_{out} = (B \cdot C_{in}) + (A \cdot B) + (A \cdot C_{in})$$

&emsp; Já para a saída 'S', percebe-se que o padrão de saída é o mesmo de uma porta XOR (Exclusico Or), com isso extraimos a expressão sem precisar utilizar o Mapa de Karnaugh:

$$S = A \oplus B \oplus Cin$$

&emsp; Com essas expressões, podemos transformar a lógica em um circuito físico utilizando portas lógicas:
- Porta XOR: Utilizadas para calcular a soma (S), pois entrega "1" apenas quando há um número ímpar de entradas ativas.
- Portas AND: Utilizadas para verificar cadas uma das expressões provenientes do Mapa de Kanaugh.
- Portas OR: Utilizadas para unir os resultados parciais e gerar o sinal final de 'Cout'.

&emsp; Essa combinação de portas garante que o circuito se comporte exatamente como previsto na tabela verdade, formando assim, nosso somador de 1 bit.

#### 1.1.3 Simulação

&emsp; Com as expressões lógicas definidas, o circuito foi montado no simulador Digital. Para a construção física dessa lógica, foram utilizadas portas XOR para a soma 'S', e uma combinação de portas AND e OR para o transporte de saída 'Cout'.

&emsp; Nesse circuito, foram utilizados túneis para conectar diferentes pontos ser sobrecarregar visualmente com fios de ligação.

<div align="center">
<img src="assets\soma_1bit.png" alt="Somador de 1 bit" width="300">
</div>

&emsp; Para verificação do circuito, podemos testar suas saídas com as combinações listadas na tabela verdade. Como podemos observar no vídeo abaixo, a integração das portas lógicas coincide com nossa tabela referente ao somador de 1 bit:

VIDEO

&emsp; Com o funcionamento do somador de 1 bit, podemos usá-lo como "célula base" para expandir a capacidade de processamento do somador para utilizar entradas de 8 bits.

### 1.2 Somador de 8 bits

&emsp; Para processar uma soma de dois números de 8 bits, é usual implementar a aqruitetura Ripple Carry Adder (Somador de Propagação), essa aqrquitetura consiste em alinhar oito unidades do somador de 1 bit.

&emsp; A lógica principal dessa arquitetura é a conexão de transporte, o 'Cout' de um bit que é conectado diretamente no Cin do bit seguinte, assim sucessivamente até o oitavo bit (MSB). Essa configuração permite a lógica do "vai-um" por toda a extenção da operação, garantindo o resultado coerente.

#### 1.2.1 Simulação

&emsp; Na simulação

## Subtrator

## Multiplicador

## Divisor

## Shift lógico

## NAND

## XOR

## ALU
