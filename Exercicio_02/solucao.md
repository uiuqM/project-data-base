# Integrantes: Jeová de Souza Barbosa e Wilque Muriel.
## Exercício 02 - CONSULTAS AVANÇADAS (UBER)

### 1 - Crie uma query que liste os motoristas com seus carros, apresentando o nome do motorista, código do motorista, nome do carro, placa e cor do carro, quantidade de estrelas do motorista, quantidade de corrida.

    SELECT motorista.id_motorista,
        motorista.nome,
        carro.modelo,
        carro.placa,
        carro.cor,
        AVG(avaliacao.nota) AS qtd_estrelas,
        COUNT(corrida.id_motorista) AS qtd_corridas
        FROM carro
            INNER JOIN motorista ON motorista.id_motorista = carro.id_motorista
            INNER JOIN avaliacao ON avaliacao.id_motorista = motorista.id_motorista
            INNER JOIN corrida ON corrida.id_motorista = motorista.id_motorista
        GROUP BY motorista.id_motorista, carro.id_carro

### 2 - Crie uma query que apresente as 4 últimas corridas de um determinado usuário, ordenado pela corrida mais cara. Campos de seleção: o código do usuário, nome do usuário, data de cadastro, destino, origem e custo da corrida, código e nome do motorista.

    SELECT usuario.id_usuario,
        usuario.nome,
        usuario.data_cadatro,
        corrida.local_origem,
        corrida.local_destino,
        corrida.preco_total,
        motorista.id_motorista,
        motorista.nome
        FROM usuario
            INNER JOIN corrida ON corrida.id_usuario = usuario.id_usuario
            INNER JOIN motorista ON motorista.id_motorista = corrida.id_motorista
        WHERE usuario.id_usuario = 2
    
    ORDER BY corrida.preco_total DESC
    LIMIT 4

### 3 - Crie uma query que liste as corridas dos usuários, apresentando os valores das corridas de um determinado mês utilizando a tabela de preço da corrida. Campos de seleção: código da corrida, código e nome do motorista, origem e destino, nome do usuário, data da solicitação, valor da corrida. O usuário precisa ter ao menos 1 corrida.

    SELECT corrida.id_corrida,
        motorista.id_motorista,
        motorista.nome_motorista,
        corrida.local_origem,
        corrida.local_destino,
        usuario.nome_usuario,
        corrida.data_hora_inicio,
        corrida.preco_total
        FROM corrida
            JOIN motorista ON motorista.id_motorista = corrida.id_motorista
            JOIN usuario ON usuario.id_usuario = corrida.id_usuario
    
    WHERE EXTRACT(MONTH FROM corrida.data_hora_fim) = 3

### 4 - Crie uma query que liste os 3 motoristas que possuem mais corridas em um determinado mês. Campos de seleção: código do motorista, nome, quantidade de corridas, valor total ganho, nome e placa do carro, ano e mês.

    SELECT corrida.id_motorista,
        motorista.nome_motorista,
        COUNT(corrida.id_motorista) AS qtd_corrida,
        SUM(corrida.preco_total) as valor_ganho,
        carro.marca,
        carro.placa
        FROM corrida
            JOIN motorista ON motorista.id_motorista = corrida.id_motorista
            JOIN carro ON carro.id_motorista = motorista.id_motorista

        WHERE EXTRACT(MONTH FROM corrida.data_hora_fim) = 3
            GROUP BY corrida.id_motorista, corrida.data_hora_fim, motorista.nome_motorista,
            carro.marca, carro.placa
        ORDER BY COUNT(corrida.id_motorista) DESC
    LIMIT 3

### 5 - Crie uma query que liste os 2 motoristas com maior avaliação nos últimos 15 dias. Campos de seleção: código e nome do motorista, avaliação, quantidade de avaliação.

    SELECT motorista.id_motorista,
        motorista.nome_motorista,
        AVG(avaliacao.nota) avaliacao,
        COUNT(avaliacao.id_motorista) qtd_de_avaliacao
        FROM motorista
            JOIN avaliacao ON avaliacao.id_motorista = motorista.id_motorista
        WHERE avaliacao.data_avaliacao > now() - INTERVAL '15' day
    
        GROUP BY motorista.id_motorista, avaliacao.nota
        ORDER BY AVG(avaliacao.nota) DESC
    LIMIT 2

### 6 - HARD#1: defina a geolocalização da corrida (origem e destino) e crie uma query que apresente a quantidade de km entre os pontos. Como a distância é em linha reta, multiplique a distância calculada por 2.13. Apresente a geolocalização da corrida (latitude e longitude), a distância em linha reta e a distância ajustada. Utilize cube e earthdistance para calcular a distância entre os estabelecimentos.

    with
    /*
    Seleciona o local origem e destino das corridas
    */
    local_origem as (
    
        select corrida.local_origem, corrida.id_corrida
        from corrida
    
    ),
    local_destino as (
    
        select corrida.local_destino, corrida.id_corrida
        from corrida
    
    )
    select local_origem,
    local_destino,
    round(cast(earth_distance(
        ll_to_earth(local_origem[0], local_origem[1]),
        ll_to_earth(local_destino[0], local_destino[1])
    )/1000 * 2.13 as numeric), 2) as distancia_km
    
    /*
    
    round(cast()) pra formatar o numero que sai de ll_to_earth().
    */
    from local_origem, local_destino
    where local_origem.id_corrida = local_destino.id_corrida
    /*
    Seleciona o id da corrida pra calcular apenas um origem/destino id da corrida
    origem = id da corrida destino
    */