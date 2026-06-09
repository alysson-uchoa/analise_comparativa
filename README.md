Projeto Power BI (Último Mês Dinâmico)
Este projeto tem como objetivo demonstrar o uso de DAX para construção de medidas dinâmicas que alternam entre valores filtrados pelo usuário e valores do último mês disponível na base, garantindo consistência em diferentes visuais.

O modelo é composto por:

1. Tabela Base: dados de vendas (quantidade e valor)
2. Tabela Calendário : suporte para análise temporal
3. Medidas para Quantidade e Valor, com variações:
4. Base (simples)
5. Geral (último mês global)
6. Individual (último mês por cliente)

- Tabela Calendário:

DAX
Calendario =
ADDCOLUMNS(
CALENDARAUTO(),

"Ano", YEAR([Date]),
"Mes", MONTH([Date]),
"Dia", DAY([Date]),

"Mes Abreviado", FORMAT([Date], "MMM"),
"Nome Mes", FORMAT([Date], "MMMM"),

"Nome Dia Semana", FORMAT([Date], "dddd"),
"Num Dia Semana", WEEKDAY([Date], 2)
)

A tabela calendário permite análises temporais, segmentações de período e controle adequado do contexto de data.

- Medidas Base

1. Quantidade - Soma simples da quantidade vendida, respeitando qualquer filtro aplicado no relatório.
Quantidade = SUM(Base[Quantidade])

2. Valor - Soma simples do valor financeiro das vendas.
Valor = SUM(Base[Valor])

3. Quantidade Geral - Retorna o valor filtrado pelo usuário quando há segmentação de mês, caso contrário, exibe automaticamente os dados do último mês da base inteira

Quantidade Geral =
VAR UltimaDataGlobal = CALCULATE(MAX('Base'[Mês]),ALL('Base'),ALL('Base'[Cliente]))

RETURN IF(ISFILTERED('Calendario'[Mes Abreviado]),[Quantidade],CALCULATE([Quantidade],'Calendario'[Mes] = UltimaDataGlobal))

4. Valor Geral - Segue a mesma lógica da medida de quantidade, porém aplicada ao valor financeiro.

Valor Geral = 
VAR UltimaDataGlobal =CALCULATE(MAX('Base'[Mês]),ALL('Base'),ALL('Base'[Cliente]))

RETURN IF(ISFILTERED('Calendario'[Mes Abreviado]),[Valor],CALCULATE([Valor],'Calendario'[Mes] = UltimaDataGlobal))

5. Quantidade Individual - Mostra o valor filtrado quando há segmentação Caso contrário, retorna a quantidade do último mês disponível para cada cliente Valor Individual

Quantidade individual =IF(ISFILTERED('Calendario'[Mes Abreviado]),[Quantidade],CALCULATE([Quantidade],ALL(Calendario),'Calendario'[Mes] = MAX('Calendario'[Mes])))

6. Valor individual - Aplica a mesma lógica ao valor financeiro, considerando o último mês individual de cada cliente.

Valor individual =IF(ISFILTERED('Calendario'[Mes Abreviado]),[Valor],CALCULATE([Valor],ALL(Calendario),'Calendario'[Mes] = MAX('Calendario'[Mes])))

O modelo permite:
- Exibir automaticamente o último período disponível
- Manter interatividade com segmentações
- Trabalhar com clientes com históricos diferentes
- Garantir consistência em totais e visuais

Esse tipo de solução é essencial em cenários onde o usuário precisa de um valor padrão confiável (último mês), mas sem abrir mão da flexibilidade de análise.
