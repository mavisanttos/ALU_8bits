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
| 1000 0000 | 80 | -128 | Início dos negativos |
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

### 1.1. Somador de 1 bit (Full Adder)

&emsp; Para a construção de um somador de 8 bits, primeiro foi necessário desenvolver um somador de 1 bit. Nele, inputamos três entradas, os dois bits a serem somados ('A' e 'B') e o bit de transporte de entrada ('Cin' ou 'Ci'). E esperamos a sapida 'S' e 'Co' correspondentes, conforme vemos na tabela verdade a seguir.

#### 1.1.1. Tabela Verdade do Somador de 1 bit

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

#### 1.1.2. Mapa de Karnaugh e Expressões Lógicas 

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

#### 1.1.3. Simulação

&emsp; Com as expressões lógicas definidas, o circuito foi montado no simulador Digital. Para a construção física dessa lógica, foram utilizadas portas XOR para a soma 'S', e uma combinação de portas AND e OR para o transporte de saída 'Cout'.

&emsp; Nesse circuito, foram utilizados túneis para conectar diferentes pontos ser sobrecarregar visualmente com fios de ligação.

<div align="center">
<img src="assets\soma_1bit.png" alt="Somador de 1 bit" width="500">
</div>

&emsp; Para verificação do circuito, podemos testar suas saídas com as combinações listadas na tabela verdade, caso a integração das portas lógicas coincida com nossa tabela referente ao somador de 1 bit, o circuito funciona perfeitamente.

&emsp; Com o funcionamento do somador de 1 bit, podemos usá-lo como "célula base" para expandir a capacidade de processamento do somador para utilizar entradas de 8 bits.

### 1.2. Somador de 8 bits

&emsp; Para processar uma soma de dois números de 8 bits, é usual implementar a arquitetura Ripple Carry Adder (Somador de Propagação), essa arquitetura consiste em alinhar oito unidades do somador de 1 bit.

&emsp; A lógica principal dessa arquitetura é a conexão de transporte, o 'Cout' de um bit que é conectado diretamente no Cin do bit seguinte, assim sucessivamente até o oitavo bit (MSB). Essa configuração permite a lógica do "vai-um" por toda a extensão da operação, garantindo o resultado coerente.

#### 1.2.1. Barramento

&emsp; Para compreensão do sitema seguinte é relevante a explicação do uso de distribuidores para barramento:
- Distribuição de Entradas: Os distribuidores conectados às entradas separam os 8 bits de cada valor. O bit 0 (A0 e B0) representa o LSB (bit menos significativo), enquanto o bit 7 (A7 e B7) representa o MSB (bit mais significativo)
- Conexão com os blocos de *soma_1bit*: Cada bit correspondente das entradas alimenta um bloco somador (A0 e B0 estão conectados no bloco 1 e assim por diante). Para continuidade da conta, a conexão do 'Co' de um bloco alimenta o 'Ci' do bloco seguinte, permitindo que o "vai-um" seja parte da próxima parte da soma total.
- Agrupamento de Saída: Para visualização dos dados, para as saídas 'S1' e 'S2' usa-se o distribuidor novamente mas dessa vez no sentido inverso, ele reúne os bits para visualizar um bit representativo apenas:
    - S1: Reúne os 4 bits menos significativos
    - S2: Reúne os 4 bits mais significativos

&emsp; Abaixo, temos a simulação utilizando os conceitos relatados acima.

#### 1.2.2. Simulador

&emsp; O circuito foi exibido de forma a exibir o resultado separado em duas partes, os 4 bits menos significativos ('S1') e os 4 bits mais significativos ('S2'). Ao final do oitavo bloco, a saída do último 'Co' representa se a operação teve um "vai-um" extra, caso o circuito tivesse suporte para mais de 8 bits.

<div align="center">
<img src="assets\soma_8bits.png" alt="Circuito Somador 8 bits" width="500">
</div>

&emsp; Assim, como utilizamos o somador de 1 bit como célula base para o somador de 8 bits, não se faz necessário repetir os Mapas de Karnaugh, uma vez que a lógica interna de cada bloco permanece a mesma, ela apenas foi escalada.

### 1.3. Registrador Acumulador (AC)

&emsp; Para que o somador consiga realizar somas sucessivas, ou seja, guardar o último resultado e usá-lo como entrada para a próxima operação, é necessário introduzir um elemento de memória capaz de armazenar o resultado, nesse cado, um Flip-Flop.

#### 1.3.1. Flip-Flop Tipo D

&emsp; O Flip-Flop Tipo D possui uma entrada de dados (D), uma entrada de controle (Clock) e uma saída (Q):

<div align="center">
<img src="assets\flip_flop_d.png" alt="Flip-Flop Tipo D" width="500">
</div>

&emsp; Ele funcina como um gatilho, toda mudança na entrada 'D' é ignorada até que receba um pulso de subida no sinal de 'Clock'. Ao receber esse pulso, ele captura o valor de 'D' e o mantém na saída Q, "memorizando" o bit mesmo que a entrada mude posteriormente.

#### 1.3.2. Flip-Flop no Registrador Acumulador

&emsp; Para o somador de 8 bits, oito Flip-Flops Tipo D são dispostos na saída de cada célula do somador de 1 bit, eles compartilham o mesmo sinal de clock e realimentam a entrada 'A' com o sinal "memorizado".

&emsp; Nesse sistema:
- Entrada de dados (B): É o valor que se deseja somar ao valor "memorizado", ele é inserido manualmente.
- Saída para a Memória: A soma resultante (S) de cada bit do somador de 8 bits é conectada à entrada D de seu respectivo Flip-Flop.
- Realimentação: A saída Q de cada Flip-Flop é conectada de volta à entrada A do somador.

&emsp; Dessa forma, o somador está sempre somando o valor atual de 'B' com o valor da memória. O resultado da memória é atualizado sempre que se pressiona o botão de Clock, assim, um pulso de subida é enviado e um novo valor é guardado na memória.

#### 1.3.3. Simulação

&emsp; Na simulação abaixo, a entrada 'A' refere-se ao valor salvo na memória enquanto o valor 'B' é o que se deseja somar com o salvo.

<div align="center">
<img src="assets\soma_AC.png" alt="Circuito Acumulador de 8 bits" width="500">
</div>

&emsp; Ao clicar no botão de 'Clock', o valor presente nas saídas 'S1' e 'S2' são atualizados e retornam para a entrada do sisitema, permitindo que o circuito incremente valores cumulativamente a cada ciclo.

## 2. Subtrator

&emsp; Para realizar a operação de subtração em um sistema digital, não é necessário construir um circuito totalmente novo e distinto do somador. Assim como na aritmética tradicional, subtrair um valor é o mesmo que somar o seu oposto (por exemplo, $10 - 4$ é o mesmo que $10 + (-4)$), na lógica binária utilizamos o mesmo princípio.

### 2.1. Subtrator de 8 bits

&emsp; Com o objetivo de otimizar o projeto, para o subtrator, utiliza-se a aritmética de Complemento de 2. Essa técnica permite utilizar a mesma estrutura do somador de 8 bits uma vez que uma das entradas seja invertida e somada a 1.

#### 2.1.1 Complemento de 2

&emsp; Em termos matemáticos, subtrair um número de outro é a mesma coisa que somar o oposto de um número com outro (A + B = A + (-B)). No sistema binário, seguimos o seguinte passo para encontrar o negativo de um número:
1. Inverter todos os bits do número (1 -> 0 e 0 -> 1), isso é conhecido como complemento de 1
2. Somar 1 ao resultado dessa inversão

&emsp; O circuito projetado realiza esses dois processos usando uma entrada de controle chamada Sel (Seleção).

#### 2.1.2. Portas XOR e 'Sel'

&emsp; Para transformar o somador em subtrator, foram feitas duas modificações principais controladas pelo sinal Sel:
- Inversão de bit: Cada bit da entrada B passa por uma porta XOR antes de chegar ao somador. A outra entrada da porta XOR está ligada ao sinal Sel.
    - Se Sel = 0: A porta XOR mantém o valor original de B (Modo Soma).
    - Se Sel = 1: A porta XOR inverte todos os bits de B (Modo Subtração).
- Soma do bit 1: O sinal 'Sel' também está conectado ao 'Ci' do primeiro bloco somador, assim, quando Sel = 1, ele soma 1 no cálculo, assim, é realizado o complemento de 2.

&emsp; Assim, em termos binários, temos a seguinte lógica, para subtrair 5-3 em 4 bits (para exemplificação):
- A = 0101 (5)
- B = 0011 (3)
&emsp; Ao ativar Sel = 1:
- B é invertido pelas portas XORs: 0011 -> 1100
- O 'Ci' do primeiro bloco recebe 1
- A conta final fica: 0101 + 1100 + 1 = 10010

&emsp; Assim, o resultado, desprezando o 'Co' (1), é 0010, que é 2 em decimal, sendo coerente com a operação 5 - 3 = 2.

OBS.: Se o resultado é positivo, ou igual a zero, o 'Co' sempre será 1, caso contrário, ele será 0.

#### 2.1.3. Simulação

&emsp; Assim, o circuito final possui dupla função. Com a entrada Sel=0, o circuito opera como somador visto anteriormente. Já com Sel=1, ele transforma a entrada 'B' em negativa e realiza uma operação de subtração.

<div align="center">
<img src="assets\subtracao_8bits.png" alt="Circuito Somador/Subtrator de 8 bits" width="600">
</div>

&emsp; Essa implemnetação otimiza ter duas operações matemáticas em um mesmo sistema.

### 2.2. Registrador Acumulador (AC)

&emsp; A integração do registrador acumulador do somador com a lógica do subtrator transforma o circuito em uma unidade capaz de realizar operações sucessivas de adição e subtração sobre um valor armazenado.
- Sinal 'Sel': Ele atua como o seletor de instrução.
    - Quando queremos acumular um valor (AC = AC + B), mantemos Sel = 0.
    - Quando queremos descontar um valor (AC = AC - B), ativamos Sel = 1.
- Clock: Independentemente da operação escolhida (soma ou subtração), o resultado só é efetivado no acumulador após o pulso de Clock, garantindo um controle de quando a operação deve ser salva na memória.

<div align="center">
<img src="assets\subtracao_AC.png" alt="Circuito Somador/Subtrator Acumulador de 8 bits" width="600">
</div>

## 3. Multiplicador

&emsp; O circuito de multiplicação de 8 bits realiza o produto de dois números binários através do método de Array Multiplier. Este circuito simula o processo de multiplicação manual, onde cada bit do multiplicador (B) gera uma linha de produtos parciais que são somados e deslocados para compor o resultado final.

### 3.1. Multiplicador de 1 bit

&emsp; A unidade básica do sistema consiste em um multiplicador de 1 bit composto por uma porta AND e um Somador de 1 bit (Full Adder).
- Lógica AND: Na aritmética binária, a multiplicação de dois bits resulta em 1 apenas quando ambos são 1 ($1 \times 1 = 1$). Para qualquer outra combinação, o resultado é 0. Essa característica é identica ao comportamento de uma porta lógica AND.

    Na multiplicação binária de 1 bit, a regra é:
    - $0 \times 0 = 0$
    - $0 \times 1 = 0$
    - $1 \times 0 = 0$
    - $1 \times 1 = 1$

    Essa é exatamente a tabela verdade de uma porta AND. Portanto, para multiplicar dois bits, usamos uma porta AND.

- Somador: Para construir um multiplicador de vários bits, o resultado de uma multiplicação ($A \times B$) precisa ser somado aos resultados parciais vindos das colunas superiores e ao "vai-um" (Carry) das colunas à esquerda. O somador de 1 bit recebe o produto da porta AND e realiza essa integração.

| A (S) | B (AXB) | Cin | S | Cout | 
| :---: | :---: | :---: | :---: | :---: |
| 0 | 0 | 0 | 0 | 0 |
| 0 | 0 | 1 | 1 | 0 |
| 0 | 1 | 0 | 1 | 0 |
| 0 | 1 | 1 | 0 | 1 |
| 1 | 0 | 0 | 1 | 0 |
| 1 | 0 | 1 | 0 | 1 |
| 1 | 1 | 0 | 0 | 1 |
| 1 | 1 | 1 | 1 | 1 |

<div align="center">
<img src="assets\multiplicacao_1bit.png" alt="Circuito Multiplicador de 1 bits" width="800">
</div>

&emsp; A integração ocorre da seguinte forma: a porta AND atua como um 'filtro' que decide se o valor de $A$ deve ser somado naquela posição da grade. Se $B$ for 0, a AND anula o valor de $A$, e o somador apenas repassa adiante a soma que veio das linhas superiores. Se $B$ for 1, a AND 'deixa passar' o valor de $A$, que é então somado ao acumulado parcial, gerando um novo resultado e, possivelmente, um bit de transporte (Co) para a próxima célula.

### 3.2. Multiplicador de 8 bits

&emsp; A partir do multiplicador de 1 bit, linhas de multiplicação são formadas, cada uma contendo 8 células do multiplicador de 1 bit. Cada linha é responsável por multiplicar o barramento A completo por um único bit do barramento B.
- Linha 0 (Base): Multiplica A0-A7 por B0. Como não há linha superior, a entrada de soma (S) de todos os somadores é conectada ao Ground (0).
- Passagem de Carry: Dentro de cada linha, o Co (Carry Out) de uma célula é conectado ao Ci (Carry In) da célula imediatamente à direita. O Ci da primeira célula de cada linha (à esquerda) é sempre 0.
- Interconexão de Linhas: A partir da Linha 1, a entrada de soma de cada célula recebe o bit de resultado da linha anterior, porém com um deslocamento à esquerda.
    - Exemplo: O $S0\_1$ da Linha 0 entra como soma na primeira célula da Linha 1.

#### 3.2.1. Saídas: AC e MQ (16 bits)

&emsp; Como o produto de dois números de 8 bits resulta em 16 bits, o resultado é dividido em dois barramentos de 8 bits
- AC (Accumulator - LSB): Coleta os bits menos significativos. Eles são formados pela primeira saída de cada linha:
    - $AC = \{S7\_0, S6\_0, S5\_0, S4\_0, S3\_0, S2\_0, S1\_0, S0\_0\}$
- MQ (Multiplier Quotient - MSB): Coleta os bits mais significativos que sobram da última linha de processamento:
    - $MQ = \{Co\_7, S7\_7, S7\_6, S7\_5, S7\_4, S7\_3, S7\_2, S7\_1\}$

#### 3.2.2. Simulação

&emsp; Na simulação abaixo, ao inserirmos os valores nos barramentos A e B, o sinal percorre a malha de somadores.

<div align="center">
<img src="assets\multiplicacao_8bit.png" alt="Circuito Multiplicador de 8 bits" width="800">
</div>

&emsp; Esse circuito não possui uma lógica de acumulador pois, com o objetivo de otimização de recursos, um conjunto de registradores acumuladores será inserido na ALU completa.

## 4. Divisor

&emsp; Para a divisão de 8 bits implementa-se um Divisor de Matriz por Restauração. O circuito funciona como uma "máquina de estados combinacional" de 8 níveis, onde cada linha tenta subtrair o divisor e, caso o resultado seja negativo, o valor original é restaurado antes de passar para a próxima linha.

### 4.1. Subtrator com Restauração

&emsp; A unidade básica para realizar a divisão é um subtrator com restauração de 1 bit, ele funciona como um subtrator e com tomador de decisões acerca do resultado (se a subtração ocorre ou se o valor original é restaurado)

#### 4.1.1. Lógica de Subtração

&emsp; Utiliza-se um somador de 1 bit adaptado para realizar operações de subtração, uma vez que uma subtração nada mais é que uma adição com múmeros negativos. Para realizar essa operação foram utilizadas três entradas:
- Entrada A: O bit do dividendo parcial.
- Entrada Não B ($\bar{B}$): O bit do divisor invertido.
- Entrada Ci (Carry In): Responsável por somar o "1" necessário para completar a lógica de complemento de 2 ou receber o transporte da célula anterior.

&emsp; A saída Co (Carry Out) deste somador indica o transporte para a próxima célula à esquerda.

#### 4.1.2. Multiplexador

&emsp; O componente central para a funcionalidade de "restauração" é o multiplexador que recebe duas opções de dados:
- Entrada 0: Conectada diretamente à entrada A (valor original).
- Entrada 1: Conectada à saída S (Soma) do somador (resultado da subtração).

#### 4.1.3. Seleção e Resultado

&emsp; O funcionamento do bloco é definido pela entrada de seleção (SEL):
- Se SEL = 1: O multiplexador valida a subtração e a saída R assume o valor calculado pelo somador ($A - B$).
- Se SEL = 0: O multiplexador ignora o resultado do somador e a saída R restaura o valor original de A.

&emsp; Esta configuração garante que o dividendo parcial nunca se torne um número "inválido" ou negativo durante as sucessivas tentativas de divisão.

<div align="center">
<img src="assets\subtracao_restauracao.png" alt="Célula de Subtração com Restauração" width="600">
</div>

### 4.2. Subtrator de 8 bits

&emsp; Para fazermos uma divisão com 8 bits, construimos uma matriz composta por 64 células organizadas em 8 linhas e 8 colunas. A conexão segue três padrões:
- Cout (Horizontal): Em cada linha, o sinal de 'Co' viaja da direita para a esquerda. O último Cout da célula mais à esquerda define o bit do quociente daquela linha. Se $Cout = 1$, a subtração foi bem-sucedida.
- Shift Left: Para simular o processo de baixar o próximo dígito da divisão manual, o resultado (R) de cada célula (saída do MUX) é passado para a célula à sua esquerda na linha de baixo na entrada 'A'.
- Controle: O bit do quociente gerado no final de uma linha é enviado de volta para todas as 8 células da mesma linha. Isso garante que todas as células decidam se devem manter a subtração ou restaurar o valor.

#### 4.2.1. Entradas e Saídas

- Dividendo (A): Posicionado bit a bit ($A_7$ a $A_0$) na coluna da extrema direita de cada linha, começando da Linha 0.
- Divisor (B): Entra de forma constante em todas as colunas da matriz.
- Quociente (MQ - Multiplier Quotient): Formado pelos 8 bits de controle coletados da ponta esquerda de cada linha. O topo da matriz gera o bit mais significativo ($MQ_7$).
- Resto (AC - Accumulator): Formado pelas 8 saídas dos MUXs da última linha (Linha 7).

<div align="center">
<img src="assets\divisor_8bit.png" alt="Divisor de 8 bits" width="600">
</div>

## 5. Shift lógico

&emsp; O deslocamento lógico é uma operação utilizada para manipulação de bits e para realizar multiplicações ou divisões rápidas por potências de 2. Nesta unidade, o circuito recebe um dado de 8 bits e o desloca para a esquerda ou para a direita conforme comando.

&emsp; Diferente das operações aritméticas, o shift lógico não utiliza portas lógicas, ele é implementado através do reordenamento das conexões de barramento:
- Shift Left (SHL): Para deslocar à esquerda, cada bit An é conectado à saída {n+1}. O bit A7 é descartado e a posição 0 da saída é conectada ao GND (Terra).

<div align="center">
<img src="assets\shift_left.png" alt="Shift Left" width="600">
</div>

- Shift Right (SHR): Para deslocar à direita, cada bit An é conectado à saída {n-1}. O bit A0 é descartado e a posição 7 da saída é conectada ao GND (Terra).

<div align="center">
<img src="assets\shift_right.png" alt="Shift Right" width="600">
</div>

### 5.1. Multiplexador

&emsp; Para permitir a escolha do deslocamento em um único circuto, utiliza-se um Multiplexador (MUX) de 8 bits. Ele funciona recebento a saída do barramento do SHL e do SHR. Um sinal de controle 'Dir' seleciona qual dos dois barramentos será direcionado para a saída final do circuito:
- Dir = 0: A saída exibe o número original deslocado para a esquerda (Shift Left).
- Dir = 1: A saída exibe o número original deslocado para a direita (Shift Right).

#### 5.1.1. Simulação

&emsp; Assim, o circuito final possui dupla função. Com a entrada Dir=0, o circuito opera como Shift Left e o valor é dobrado. Já com Dir=1, ele opera como Shft Right e o valor é reduzido à metade. O uso do multiplexador permite essa alternância de forma imediata.

<div align="center">
<img src="assets\shift.png" alt="Shift" width="600">
</div>

&emsp; Esta implementação otimiza o funcionamento do Shift em apenas um circuito, sem consumo extra de processamento lógico.

## 6. NAND

&emsp; A porta NAND (Not-AND) é capaz de reproduzir qualquer outra função lógica. Nesta unidade, a operação é realizada bit a bit, comparando cada bit da entrada A com o bit correspondente da entrada B.

### 6.1. Tabela Verdade da Porta NAND de 1 bit

&emsp; A lógica NAND resulta 0 apenas quando ambas as entradas são 1. Caso contrário, a saída será sempre 1. Ela pode ser associada como o contrário da porta AND.

| A | B | Saída (NAND) |
| :---: | :---: | :---: |
| 0 | 0 | 1 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

### 6.2. Simulação

&emsp; Para processar 8 bits simultaneamente, o circuito utiliza entradas de 8 bits do simulador que são conectadas diretamente a uma única porta NAND configurada para a mesma largura de bits.
- Lógica da Saída: Se inserirmos, por exemplo, o valor hexadecimal 01 em ambas as entradas (A e B):
    - Os bits de 7 a 1 serão 0 NAND 0, resultando em 1.
    - O bit 0 será 1 NAND 1, resultando em 0.
        - Ao aplicar o valor hexadecimal 01 em ambas as entradas, a porta NAND realiza a lógica 1 NAND 1 no bit 0 (resultando em 0) e 0 NAND 0 nos bits de 1 a 7 (resultando em 1).
    - O resultado final em binário será 1111 1110, que corresponde ao valor FE em hexadecimal.

<div align="center">
<img src="assets\nand.png" alt="Circuito Porta NAND com 8 bits" width="600">
</div>

&emsp; Essa unidade serve como base para a construção de funções lógicas mais complexas e para a manipulação seletiva de bits em dados de 8 bits.

## 7. XOR

&emsp; A operação XOR (Exclusive OR) é utilizada para identificar diferenças entre dois sinais. Diferente da porta OR, ela resulta 1 apenas quando as entradas possuem valores distintos entre si.

### 7.1. Tabela Verdade da Porta XOR de 1 bit

&emsp; A lógica XOR resulta em 1 sempre que os bits de entrada forem diferentes. Se os bits forem iguais (ambos 0 ou ambos 1), a saída será 0.

| A | B | Saída (XOR) |
| :---: | :---: | :---: |
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

### 7.2. Simulação

&emsp; A entrada de 8 bits é conectada diretamente a uma porta XOR de largura correspondente, processando a comparação de todos os bits de forma simultânea.
- Lógica da Saída: Se inserirmos, por exemplo, os valores hexadecimais E e A nas entradas:
    - 0E em binário é 0000 1110
    - 0A em binário é 0000 1010
        - Comparando bit a bit: os bits iguais resultam em 0 e apenas o bit 2 (que é diferente: 1 XOR 0) resulta em 1.
        - O resultado final em binário será 0000 0100, que corresponde ao valor 04 em hexadecimal.

<div align="center">
<img src="assets\xor.png" alt="Circuito Porta XOR com 8 bits" width="600">
</div>

&emsp; Esta operação é utilizada para verificar a igualdade entre dois números ou para realizar a inversão seletiva de bits.

## 8. ALU

&emsp; A etapa final do projeto consiste em integrar todos os módulos descritos acima em uma única unidade funcional (ALU). Essa unidade final utiliza um Registrador Acumulador para armazenar resultados e permitir operações sucessivas.

### 8.1. Registrador

&emsp; Nesse circuito, a entrada manual "A" dos operadores com duas entradas foi substituída pelo valor Q, proveniente da saída do registrador:
- Entrada B: Permanece como uma entrada manual para o usuário inserir o novo dado.
- Entrada Q (Acumulador): Alimenta automaticamente a segunda entrada de todos os blocos operacionais.
- Funcionamento: O resultado de uma operação (ex: Soma) é calculado instantaneamente, mas só é "salvo" e enviado de volta para a entrada dos blocos quando o usuário pressiona o botão =.
    - Ao pressionar "=", ocorre uma borda de subida que captura o valor na saída do multiplexador

### 8.2. Multiplexador

&emsp; Para gerenciar as diversas saídas dos blocos, foi utilizado um Multiplexador de 8 bits. A seleção da operação é feita através do botão OP (Operação), que define qual resultado será direcionado para a entrada do registrador.

| Operação (Binário) | Operação | Fonte do Dado | Função |
| :---: | :---: | :---: | :---: |
| 0000 | Soma | Somador (S1 e S2) | Q+B→Q |
| 0001 | Subtração | Subtrator (Compl. 2) | Q−B→Q |
| 0010 | Mult. LSB | Multiplicador (AC) | Parte baixa do produto |
| 0011 | Mult. MSB | Multiplicador (MQ) | Parte alta do produto |
| 0100 | Div. Quociente | Divisor (MQ) | Resultado da divisão |
| 0101 | Div. Resto | Divisor (AC) | Resto da divisão |
| 0110 | Shift Left | Deslocamento Esq. | Dobra o valor de Q |
| 0111 | Shift Right | Deslocamento Dir. | Divide Q por 2 |
| 1000 | NAND | Porta NAND 8 bits | Lógica Universal |
| 1001 | XOR | Porta XOR 8 bits | Comparação de bits |

### 8.3. Saída de dados

&emsp; Para a leitura do acumulador, a saída Q de 8 bits é enviada para um distribuidor que separa o barramento em dois grupos de 4 bits: o grupo S2 (bits 0-3) representa o dígito menos significativo e o grupo S1 (bits 4-7) o mais significativo.

<div align="center">
<img src="assets\alu_8bit.png" alt="ALU de 8 bits" width="600">
</div>

[Video para demonstração do funcionamento](https://youtu.be/OaFYrV307hE)

&emsp; A implementação desta ALU de 8 bits demonstrou como portas lógicas simples podem ser escalonadas para realizar operações matemáticas. Desde a construção da célula básica do somador até a matriz de divisão com restauração e o laço de realimentação do acumulador, o projeto reflete a arquitetura fundamental de um processador real, capaz de processar, armazenar e manipular dados de forma eficiente.
