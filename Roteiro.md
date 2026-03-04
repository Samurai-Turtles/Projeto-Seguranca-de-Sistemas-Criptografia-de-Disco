# Roteiro

## 1. Introdução

Dar uma leve introdução à criptografia de disco:

- Explicar o que é FDE (Full Disk Encryption)
- Falar (bem por cima) sobre os benefícios do FDE: confidencialidade, integridade
- Destacar que integridade não é o foco principal, sendo "melhor elaborado" apenas em algumas implementações.
- Falar sobre os casos de uso do FDE: em que casos de uso ele seria útil.

## 2. Trade-offs

- Elaborar mais sobre benefícios do FDE: o principal sendo segurança/confidencialidade de dados em repouso.
- Falar também sobre impactos negativos no desempenho e (por cima) sobre os riscos atrelados, como perda de dados.
- Seria interessante reunir diversos benefícios e riscos.

## 3. Evolução

- Fazer um comparativo entre soluções históricas e abordagens modernas
- Destacar benefícios e malefícios de cada abordagem
- Destacar como abordagens modernas são melhores em relação às antigas
- É interessante também mostrar exemplos de abordagens

## 4. Algoritmos

- Dar uma visão geral (rápida) dos algoritmos vistos em sala
- Explicar melhor o algoritmo AES-XTS (como é melhor que os demais, como é pior, e seu funcionamento interno, porque virou o padrão moderno):
  - Tweak: dar um exemplo simples de uso.
  - Paralelismo e alta performance: explicar como o XTS garante paralelismo e, como consequência, alta performance.

## 5. Ferramentas de mercado

- Exemplificar algumas ferramentas usadas no mercado e fazer um comparativo entre elas.
- Comparar as ferramentas sob vários aspectos
- Destacar se há uma solução objetivamente melhor que a outra ou não.

## 6. Recuperação de disco e riscos teóricos

- Falar em mais detalhes sobre riscos e impactos atrelados ao FDE. O principal deles são falhas de hardware.
- Não é exatamente um risco, mas falar sobre o trade-off entre recuperabilidade e privacidade, exemplificando se necessário.

## 7. Modalidades de implementação

- Dar uma introdução rápida sobre as diversas modalidades de implementação

## 8. Demonstração

- Mostrar a criptografia na prática com os métodos escolhidos.
