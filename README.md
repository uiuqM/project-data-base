# project-data-base
Repositório da discplina de projeto de banco de dados semestre 2023.1
## Projeto UBER

## Exercício 01

1. Criar tabelas de Usuário, Motorista, Carro, Corrida, Avaliação e comentário da corrida, Tabela de preço da corrida.
2. Popular banco de dados com:
  * 5 usuários aleatórios
  * 5 motoristas com 1 carro cada
  * 5 corridas passadas com avaliação e comentário
  * 5 corridas passadas sem avaliação e comentário
3. tabela de preço da corrida (taxa fixa de início da corrida: 5 reais):

k_inicio|km_final   |taxa
--------|-----------|------
0  km   |5.999 km   |2.5 o km
6  km   |10.999 km  |2.35 o km
11 km   |           | 2.15 o km

* Obs: Popular o banco quando for necessário para apresentar os resultados.

### Exercício 2 - consultas avançadas

1. Crie uma query que liste os motoristas com seus carros, apresentando o nome do motorista, código do motorista, nome do carro, placa e cor do carro, quantidade de estrelas do motorista, quantidade de corrida. 
2. Crie uma query que apresente as 4 últimas corridas de um determinado usuário, ordenado pela corrida mais cara. Campos de seleção: o código do usuário, nome do usuário, data de cadastro, destino, origem e custo da corrida, código e nome do motorista. 0
3. Crie uma query que liste as corridas dos usuários, apresentando os valores das corridas de um determinado mês utilizando a tabela de preço da corrida. Campos de seleção: código da corrida, código e nome do motorista, origem e destino, nome do usuário, data da solicitação, valor da corrida. O usuário precisa ter ao menos 1 corrida.
4. Crie uma query que liste os 3 motoristas que possuem mais corridas em um determinado mês. Campos de seleção: código do motorista, nome, quantidade de corridas, valor total ganho, nome e placa do carro, ano e mês. 0.9
5. Crie uma query que liste os 2 motoristas com maior avaliação nos últimos 15 dias. Campos de seleção: código e nome do motorista, avaliação, quantidade de avaliação. 
6. HARD#1: defina a geolocalização da corrida (origem e destino) e crie uma query que apresente a quantidade de km entre os pontos. Como a distância é em linha reta, multiplique a distância calculada por 2.13. Apresente a geolocalização da corrida (latitude e longitude), a distância em linha reta e a distância ajustada. Utilize cube e earthdistance para calcular a distância entre os estabelecimentos.
7. HARD#2: utilize a extensão de pesquisa full text search e crie uma query que permita a busca por termos similares apresentando as contas e posts que contém um determinado termo, com similaridade a partir de 0.7. Campos de seleção: código da entidade, nome da tabela da entidade, campo de busca.

Obs: Popular o banco quando for necessário para apresentar os resultados.

### Exercício 3 - procedures e functions

1. Precisamos de uma função para calcular o valor da corrida, na qual receberá por parâmetro a quantidade de quilômetros e retornará o valor. Utilize a tabela de preço da corrida. (implementar a lógica de pegar o valor correto do km).
2. Criar uma procedure que atualize o valor do preço total das corridas utilizando a tabela de preço da corrida. Utilize a função para pegar o preço.
Nova funcionalidade: O carro pode ser de 3 tipos: básico, médio e luxo. Ajustar a tabela e os dados no banco, populando o banco de dados com pelo menos uma corrida para cada tipo.
4. Com a nova funcionalidade de tipo de carro, a forma de calcular sofrerá um ajuste. Para isso, crie OUTRA função para calcular o preço da corrida levando em consideração o tipo do carro.
Fórmula de cálculo pelo tipo do carro:
 * Carro básico: multiplicar o valor x 1.153
 * Carro médio: multiplicar o valor x 1.379
 * Carro luxo: multiplicar o valor x 1.586
5. Replique a procedure do item 2 e atualize-a utilizando a nova função do item 4.
