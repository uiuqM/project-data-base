# Integrantes: Jeová de Souza Barbosa e Wilque Muriel.

## Exercício 3 - procedures e functions

### 1 - Precisamos de uma função para calcular o valor da corrida, na qual receberá por parâmetro a quantidade de quilômetros e retornará o valor. Utilize a tabela de preço da corrida. (implementar a lógica de pegar o valor correto do km).

    create or replace function calc_preco(distancia double precision)
        returns table(valor double precision)
    language plpgsql
    as
    $$
    declare valor real;
    begin
    return query
    
        select distancia*taxa+5
            from preco_corrida
        where distancia > preco_corrida.km_inicio and distancia <= preco_corrida.km_final;
    
    END;
    $$;

### 2 - Criar uma procedure que atualize o valor do preço total das corridas utilizando a tabela de preço da corrida. Utilize a função para pegar o preço.

    create or replace procedure add_column_val_calc()
    language plpgsql
    as
    $$
    begin
        update corrida set preco =(select calc_preco from calc_preco(corrida.distancia_km));
    end;
    $$;

### 3 - Nova funcionalidade: O carro pode ser de 3 tipos: básico, médio e luxo. Ajustar a tabela e os dados no banco, populando o banco de dados com pelo menos uma corrida para cada tipo.

    create table tipo_carro(
        id int generated by default as identity,
        categoria varchar(14) not null,
        primary key(id)
    );
    insert into tipo_carro(categoria) values
        ('básico'),
        ('médio'),
        ('luxo');
    alter table carro add column categoria int references tipo_carro(id)

### 4 - Com a nova funcionalidade de tipo de carro, a forma de calcular sofrerá um ajuste. Para isso, crie OUTRA função para calcular o preço da corrida levando em consideração o tipo do carro. Fórmula de cálculo pelo tipo do carro:
|Tipo        |  preço a ser calculado      |
|------------|-----------------------------|
|Carro básico|  multiplicar o valor x 1.153|
|Carro médio |  multiplicar o valor x 1.379|
|Carro luxo  |  multiplicar o valor x 1.586|

    create or replace function calc_preco_novo(distancia double precision, cat integer)
        returns table(valor double precision)
    language plpgsql
    as
    $$
    declare
        valor real;
    begin
    return query
        select round((distancia*taxa)+5)*tipo_carro.taxa_carro
        from preco_corrida, tipo_carro
        where distancia > preco_corrida.km_inicio and distancia <= preco_corrida.km_final and cat =
        tipo_carro.id;
    END;
    $$;

### 5- Replique a procedure do item 2 e atualize-a utilizando a nova função do item 4.

    create or replace procedure add_column_val_calc()
    language plpgsql
    as
    $$
    begin
        update corrida set preco =(select calc_preco_novo from
        calc_preco_novo(corrida.distancia_km, carro.categoria))
    
        from carro
        join tipo_carro on tipo_carro.id = carro.categoria;
    
    end;