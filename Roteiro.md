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

## 3. Evolução

- Fazer um comparativo entre soluções históricas e abordagens modernas
- Destacar como abordagens modernas são melhores em relação às antigas e o porquê
- Tentar dar mais um viés histórico, reunir datas de quando essas evoluções ocorreram para situar os ouvintes na história

## 4. Algoritmos

- Dar uma visão geral (rápida) dos algoritmos vistos em sala
- Explicar melhor o algoritmo AES-XTS (como é melhor que os demais, como é pior, e seu funcionamento interno, porque virou o padrão moderno):
  - Tweak: explicar o que é e a sua funcionalidade no algoritmo.
  - Paralelismo e alta performance: explicar como o XTS garante paralelismo e, como consequência, alta performance.
  (A depender do tempo da apresentaçãp, podemos ainda explicar brevemente acerca do cypher stealing do AES-XTS)

## 5. Ferramentas de mercado

- Exemplificar algumas ferramentas usadas no mercado e fazer um comparativo entre elas.
- Comparar as ferramentas sob vários aspectos
- Destacar se há uma solução objetivamente melhor que a outra ou não e o porquê de uma ser melhor que outra em um certo aspecto, tentar pensar nessa questão dado contextos de uso diferentes.

## 6. Recuperação de disco e riscos teóricos

- Falar em mais detalhes sobre riscos e impactos atrelados ao FDE. O principal deles são falhas de hardware.
- Não é exatamente um risco, mas falar sobre o trade-off entre recuperabilidade e privacidade, exemplificando se necessário.

## 7. Modalidades de implementação

- Dar uma introdução rápida sobre as diversas modalidades de implementação
- A ideia aqui é introduzir brevemente as formas que podemos utilizar para cifrar o disco em diversos contextos, para então,
fecharmos em 2 abordagens com contextos bem específicos que devemos apresentar.

## 8. Demonstração

- Mostrar a criptografia na prática com os métodos escolhidos.
- A depender do tempo disponível, teremos que escolher apresentar só metade da workshop, se esse for o caso então vamos
apresentar só a parte de criptografia de disco no contexto da cloud por ser mais complexo e interessante em termos teóricos
