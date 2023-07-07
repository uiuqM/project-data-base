# Integrantes: Jeová de Souza Barbosa e Wilque Muriel.
## Exercício 4 - views and triggers

### 1. O time de marketing está precisando de informações sobre os carros cadastrados no aplicativo. Para isso, crie uma view que apresente o código e modelo, marca, cor e ano do carro, bem como o código do proprietário e a média de avaliação do mesmo.

    create or replace view carros_cadastrados_no_app
        as select
        carro.id_carro,
        carro.modelo,
        carro.marca,
        carro.cor,
        carro.ano,
        motorista.id_motorista,
        avg(avaliacao.nota)
        from motorista
            join carro on carro.id_carro = motorista.id_carro
            join avaliacao on avaliacao.id_motorista = motorista.id_motorista
            group by motorista.id_motorista, carro.id_carro

### 2. Com a nova forma de cálculo, será preciso auditar as alterações nos preços das corridas, permitindo verificar possíveis problemas. Assim, quando houver alguma alteração no valor da corrida, devemos salvar isso. Crie uma tabela auxiliar e armazene o código da corrida, o valor anterior e novo valor, bem como o momento da ocorrência.

    CREATE TABLE alteração_prec_corridas (
        id_alteracao SERIAL PRIMARY KEY,
        id_corrida integer NOT NULL,
        preco_antigo double precision NOT NULL,
        novo_preco double precision NOT NULL,
        data_hora timestamp NOT NULL DEFAULT NOW(),
        FOREIGN KEY (id_corrida) REFERENCES corrida (id_corrida)
    )
    
    create or replace function audit_alt_preco_corridas() returns trigger as $trigger_alt_preco_corridas$
    begin
        insert into alteracao_prec_corridas(id_corrida, preco_antigo, novo_preco, data_hora)
        values(new.id_corrida, old.preco, new.preco, current_timestamp);
        return new old;
    end;
    $trigger_alt_preco_corridas$ language plpgsql;
    create trigger trigger_alt_preco_corridas
        after insert or update or delete on corrida
    for each row execute function audit_alt_preco_corridas();