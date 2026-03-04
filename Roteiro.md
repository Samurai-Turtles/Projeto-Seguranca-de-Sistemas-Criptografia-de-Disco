# Roteiro

## 1. Introdução

Falar sobre uma leve introdução à criptografia de disco, destacando os seguintes pontos:

- explicar o que é FDE e dizer que ele visa a proteção de dados
- dizer os benefícios (por exemplo, protege os dados em caso de roubo ou de ataques de terceiros)
- explicar que integridade não é o foco principal

## 2. Trade-offs

Falar sobre os benefícios e possíveis riscos do uso de FDE

**_TODO: Falar mais benefícios e riscos?_**

### Benefícios

- explicar como ele garante a segurança de dados em repouso

### Riscos

- porém causa pequenos impactos no desempenho de operações (CPU e IO)
- explicar riscos de perda de dados em caso de esquecimento da senha ou corrupção

## 3. Evolução

**_TODO: falar sobre exemplos de implementações antigas e modernas_**

Falar sobre uma linha do tempo de implementações, explicando que as antigas eram baseadas apenas em software e tinham processamento serial (AES-CBC entra aqui?). Também falar que abordagens modernas usam AES-XTS e instruções de hardware

## 4. Algoritmos

Dar uma visão geral (bem rápida) dos algoritmos (AES-CBC e CTR) como vimos em sala. Explicar em mais detalhes o XTS (seus benefícios em relação aos demais (riscos tbm?), sistema de dupla chave, tweak)

Sobre o Tweak: Use um exemplo simples. "Se você salvar um arquivo com 1000 letras 'A' seguidas, um algoritmo antigo poderia gerar blocos cifrados repetidos, dando pistas a um atacante. O AES-XTS usa a posição (o setor do disco) como ingrediente da matemática, fazendo com que cada bloco pareça um ruído totalmente diferente, mesmo que o texto original seja o mesmo."

Sobre o Paralelismo: Lembre a sua audiência do Slide 3. "Como o XTS não precisa que o bloco 1 termine para começar a calcular o bloco 2, nós conseguimos paralelizar o processo. É isso que permite que seu computador leia e grave gigabytes por segundo num SSD criptografado sem travar a máquina."

Alta Performance (Paralelismo): Permite o processamento de múltiplos blocos de dados simultaneamente (independência de blocos), eliminando gargalos de I/O e extraindo o máximo de SSDs modernos e aceleração via hardware (ex: AES-NI).

## 5. Ferramentas de mercado

Falar sobre o LUKS, BitLocker, Cloud Providers. Comparar eles (n tem muita coisa diferente do slide aqui).

## 6. Recuperação de disco e riscos teóricos

Falar sobre riscos de falhas de hardware e seus impactos.
Falar em detalhes sobre o trade-off entre recuperabilidade e privacidade. Dar exemplos.

## 7. Modalidades de implementação

Falar sobre implementação durante a instalação e pós-instalação.
Destacar comparações / trade-offs e exemplificar.

## 8. Demonstração

Mostrar rodando em sistemas linux e em um cloud provider.
