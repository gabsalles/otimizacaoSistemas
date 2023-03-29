# Otimização do planejamento da manutenção de sistemas de ar de caminhões

Uma empresa terceirizada de transporte de caminhões viu a necessidade de otimizar seus 
gastos com a manutenção do sistema de ar de seus veículos. Apesar de a frota ter mantido a 
média de caminhões constante, nos últimos anos, os gastos com esse problema aumentaram 
constantemente, chegando a R$37.000,00 no ano atual.

![Gráfico de gastos](imagensReadme/graficogastos.png)

Quanto aos custos de manutenção para os sistemas de ar dos caminhões, se destacam as três modalidades:
* R$10 para revisões **preventivas** nas quais não é encontrado nenhum problema;
* R$25 para revisões **preventivas** nas quais é identificado um problema no sistema de ar e que necessita de manutenção;
* R$500 para manutenções **corretivas** no sistema de ar, quando o veículo não passou por uma revisão preventiva,
incluindo custos de peças, mão de obra, etc.

Espera-se que com utilização de modelos preditivos seja possível reduzir
os custos com as manutenções em geral dos sistemas de ar, principalmente antecipando a revisão de veículos
que já estejam com algum problema em seu sistema de ar e necessite de uma vistoria preventiva, evitando que o 
número de veículos que requerem manutenções corretivas seja excessivo.

A análise completa desse problema junto de sua descrição pode ser encontrada no link: [Análise de Otimização](./analise/)

## Questões levantadas sobre o projeto

Questionamentos pertinentes sobre a implementação de técnicas de ciência de dados 
sempre são levantadas em diversos projetos, e com esse não é diferente. Cada projeto tem 
suas necessidades e sua própria identidade quanto ao que deve ser feito. A seguir algumas 
questões sobre a elaboração e desenvolvimento serão respondidas, a fim de tornar o processo 
analítico dessa análise mais compreensível e replicável.

### 1. *Quais passos você realizaria para solucionar este problema? Por favor descreva da forma mais completa e clara possível todos os passos que você enxerga como essenciais para a resolução do problema.*


* **Entender o projeto como um todo**: O projeto lida com um gasto com manutenção de sistema de ar de veículos que sucessivamente, ano após ano, vem aumentando. Sabendo os custos de cada nível manutenção, é possível inferir que o momento necessário para a realização de uma revisão preventiva não tem sido alvo de atenção, já que cada uma das manutenções corretivas custa $500;
* **Explorar os dados para ganhar insights**: Como o dataset disponibilizado possui os registros de casos positivos e negativo de manutenção do sistema de ar, saber como a proporção de veículos de distribui é essencial para estabelecer o cálculo aproximado de quanto dos $37000 corresponde aos veículos que realizaram manutenção corretiva. Como o objetivo principal é reduzir o número dessas manutenções, ter esse número em mãos é fundamental;
* **Preparação, limpeza e transformação dos dados para melhor entendimento de sua distribuição**: Explorar a tabela em si, sua dimensionalidade (que nesse caso é bem robusta), os datatypes de cada uma das colunas, split entre os sets de treino e teste...Todas essas etapas geram ideias para como implementar o modelo da melhor maneira além de visar salvar o uso excessivo de recursos computacionais;
* **Modelos, Treinamento e fine-tuning dos hyperparâmetros**: A parte mais trabalhosa se é bem trabalhada é essa. A dinâmica e a escolha de modelos e parâmetros avaliados irão indicar a escolha ideal para o modelo final. Como é um projeto de classificação que visa reduzir a quantidade de veículos que necessitam de manutenção corretiva, as métricas de avaliação do modelo precisam ser pensadas para se adequar a essas necessidades;
* **Aplicação do modelo no test set**: A parte que define o sucesso no desenvolvimento do projeto, no qual o modelo é aplicado na tabela de teste, retornando as métricas que indicam que o projeto ocorreu da maneira correta, podendo assim também comparar valores que seriam gastos numa situação comum e os valores que poderiam ser gastos com a implementação do modelo.

### 2. *Qual métrica técnica de data science você utilizaria para solucionar este desafio? Ex: erro absoluto, rmse, etc.*

O problema lida com a necessidade de antecipar uma eventual falha no sistema de ar dos caminhões, 
portanto há a necessidade de minimizar o máximo possível esse número.
Em uma *confusion matrix* é possível atribuir os possíveis retornos de um modelo de classificação do projeto se fosse implementado:

![Confusion Matrix](/imagensReadme/confusionM.jpg)
- Os verdadeiros negativos (TN) corresponderiam aos veículos que foram previstos corretamente como aqueles que não precisam de revisão, portanto sem custo;
- Os verdadeiros positivos (TP) corresponderiam aos veículos que corretamente foram previstos como aqueles que necessitam de manutenção preventiva, portanto custando $25;
- Os falsos negativos (FN) sendo aqueles que erroneamente foram classificados como sem problemas, quando na verdade precisavam de revisão. Estes são aqueles que custarão $500;
- Os falsos positivos (FP) sendo aqueles que erroneamente foram classificados como necessitado de revisão, porém, como não possui problemas, haverá o gasto de apenas $10 nesses casos.

A proporção de verdadeiros positivos nos modelos será sempre alta, devido ao dataset conter 
apenas aproximadamente 2.35% dos casos de manutenção do sistema de ar. Dado a isso, a métrica de *specificity* pode não ser uma boa escolha.

Porém, como é um caso em que se visa a redução de valores de FN, a métrica ideal para monitorar essa razão seria *precision*.
Além disso, como não é recomendável um valor excessivo de FP mesmo sendo um destino barato financeiramente, é necessário monitorar
essa razão com a métrica de *recall*.

E englobando todos os valores da *confusion matrix*, a *accuracy*, para avaliar o desempenho geral do modelo.

### 3. *Qual métrica de negócio você utilizaria para solucionar o desafio?*

Métricas de negócios interessantes para o projeto, como ele visa diminuir os 
gastos com manutenção dos sistemas de ar dos caminhões, seriam a comparação dos gastos mês a mês *MoM* 
ou ano a ano *YoY* antes e depois da implementação do modelo preditivo. Isso permite avaliar o impacto financeiro do modelo e sua eficácia na redução de custos ao longo do tempo.

### 4. *Como as métricas técnicas se relacionam com a de negócio?*

Ao comparar *MoM* ou *YoY* dos valores que seriam gastos na manutenção dos sistemas, e como é um 
caso em que se visa a redução de valores de FN’s, sendo *precision* a métrica ideal para monitorar 
a proporção desses elementos, quanto maior é o *precision*, menor será a proporção de valores gastos com a manutenção corretiva.

### 5. *Quais tipos de análises você gostaria de realizar na base de dados do cliente?*

Seria fundamental a realização de uma análise exploratória de dados (EDA) para entender a distribuição dos dados e possíveis 
relações entre as variáveis, além de identificar possíveis outliers e valores faltantes. Em seguida, seria necessário 
aplicar técnicas de ML para prever as falhas no sistema de ar dos veículos, utilizando algoritmos de classificação e, 
assim, criar um modelo mais preciso para prever as falhas. Essas análises permitiriam antecipar os veículos que possam 
ter falhas no sistema de ar dos caminhões e sugerir ações de manutenção preventiva para evitar futuros problemas.

### 6. *Quais técnicas você utilizaria para reduzir a dimensionalidade do problema?*

Uma técnica que poderia ser utilizada para reduzir a dimensionalidade do 
problema seria a análise de componentes principais (PCA).

### 7. *Quais técnicas você utilizaria para selecionar variáveis para seu modelo preditivo?*

Neste projeto em específico o PCA já realiza essa seleção ao encontrar as combinações lineares que melhor explicam a variação nos dados. Contudo, é importante lembrar que o resultado final do PCA ainda dependerá das variáveis originais utilizadas como entrada.

### 8. *Quais modelos preditivos você utilizaria ou testaria para este problema? Por favor indique pelo menos 3.*

Alguns modelos preditivos que poderiam ser utilizados ou testados 
para este problema são: regressão logística, árvore de decisão e 
random forest.

### 9. *Como você avaliaria qual dos modelos treinados é o melhor?*

Como a intenção do projeto é otimizar a  *accuracy*, *recall* e *precision*, 
uma técnica eficiente é representá-las como um trio ordenado em um plano 
de 3 dimensões, onde cada ponto é um modelo treinado. 
O objetivo é encontrar o ponto ideal cuja distância euclidiana até o 
ponto (1, 1, 1) é a menor possível. Essa abordagem permite visualizar a 
performance dos modelos de forma mais clara e possibilita a seleção 
daqueles que melhor atendem às necessidades do projeto.

### 10. *Como você explicaria o resultado de seu modelo? É possível saber quais variáveis são mais importantes?*

O resultado do modelo pode ser explicado pelas métricas de avaliação, 
como acurácia, precisão e recall. Quando se usa o PCA, as variáveis 
originais são combinadas linearmente em novas variáveis que melhor 
explicam a variação dos dados, tornando difícil identificar diretamente 
a importância de cada variável. No entanto, pode-se avaliar a contribuição 
de cada componente do PCA para a variância total dos dados, o que 
permite considerar as componentes mais importantes.

### 11. *Como você avaliaria o impacto financeiro do modelo proposto?*

É necessário comparar os gastos previstos pelo modelo com os gastos reais registrados 
antes da sua implementação. A diferença entre esses valores pode indicar o potencial 
de economia gerado pelo modelo. É importante lembrar que essa avaliação deve ser feita 
em conjunto com outras considerações, como o custo de implementação e manutenção do modelo.

### 12. *Quais técnicas você utilizaria para realizar a otimização de hiperparâmetros do modelo escolhido?*

Uma abordagem interessante para o projeto seria o uso do *Grid Search*, 
que consiste em definir uma grade com possíveis valores para cada 
hiperparâmetro e testar todas as combinações possíveis. 
Apesar de exigir dos recursos computacionais, o *Grid Search* permite 
avaliar todas as combinações possíveis e encontrar os melhores valores 
de hiperparâmetros para o modelo.

### 13. *Quais riscos ou cuidados que você levaria para o cliente antes de colocar este modelo em produção?*

Ao colocar o modelo em produção, é importante considerar o risco de viés e resultados incorretos devido a 
mudanças nos dados de entrada. É fundamental manter e atualizar regularmente o modelo para garantir sua eficácia. 
Também é essencial garantir a segurança e a privacidade dos dados usados no modelo.

### 14. *Caso o seu modelo preditivo seja aprovado, como você colocaria ele em produção?*

É importante estabelecer uma API que permita o envio dos dados para o modelo e recebimento das previsões em tempo real. 
Além disso, estabelecer processos claros para atualização e manutenção contínua do modelo. O uso de gerenciadores 
de contêineres como o Kubernetes pode ajudar a gerenciar o modelo em escala e garantir a eficiência da aplicação.

### 15. *Caso o modelo esteja em produção, como você faria seu companhamento?*

É importante fazer testes regulares para garantir que o modelo continue preciso e eficaz ao longo do tempo. 
É fundamental ter um plano de manutenção para atualizar o modelo conforme necessário e corrigir quaisquer problemas que possam surgir. 
O monitoramento contínuo do desempenho do modelo ajudará a identificar possíveis problemas e garantir que as previsões sejam precisas e confiáveis.

### 16. *Caso o modelo esteja em produção, como você saberia o momento de retreinar o modelo*

Seria necessário monitorar constantemente a precisão do modelo e verificar se ela está diminuindo ao longo do tempo, 
no caso, o aumento nas despesas com a manutenção de sistema de ar dos caminhões. Além disso, seria importante avaliar 
a disponibilidade de novos dados que possam melhorar a qualidade do modelo, bem como considerar mudanças nos requisitos 
do projeto que possam exigir a atualização do modelo. A frequência ideal de retreinamento dependeria do quão variável 
fosse a margem de gastos.