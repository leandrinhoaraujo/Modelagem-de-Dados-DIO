# Modelagem Dimensional com a Financial Sample

## Sobre o projeto

Este projeto foi desenvolvido para praticar modelagem dimensional no Power BI. A partir da tabela `Financials`, organizei os dados em um esquema estrela para facilitar a análise de vendas, produtos, descontos, mercados e datas.

A base usada foi o arquivo Financial Sample disponibilizado pela Microsoft. Como ela já vem pronta, o trabalho foi concentrado em transformar e organizar os dados para o modelo analítico.

## Ferramentas

- Power BI Desktop
- Power Query
- DAX

## Tabelas do modelo

A tabela `Financials_origem` foi mantida como cópia de apoio e ocultada na exibição de relatório. As demais tabelas foram criadas a partir dela no Power Query.

- **`F_Vendas`**: tabela fato com os registros de venda, identificador da linha, chaves das dimensões e valores como vendas, descontos, custo e lucro.
- **`D_Produtos`**: reúne os produtos e seus indicadores agregados, como média de unidades vendidas, média, mediana, valor mínimo e valor máximo de venda.
- **`D_Produtos_Detalhes`**: guarda informações detalhadas do produto relacionadas à faixa de desconto, preço de venda, unidades vendidas e preço de fabricação.
- **`D_Descontos`**: organiza os valores de desconto e suas faixas.
- **`D_Detalhes`**: reúne os atributos de mercado `Segment` e `Country`.
- **`D_Calendario`**: tabela de datas com ano, mês, número do mês, trimestre e ano-mês.

As tabelas de dimensão foram ligadas diretamente à `F_Vendas` por suas chaves. Os relacionamentos foram configurados como um para muitos, com filtro em uma direção, formando o esquema estrela.

## Etapas de construção

1. Carreguei a tabela `Financials` no Power BI.
2. Ajustei os tipos de dados no Power Query, mantendo datas como data, categorias como texto e valores monetários como número decimal fixo.
3. Mantive a tabela original como backup e criei consultas de referência para as novas tabelas.
4. Agrupei os dados por produto e calculei indicadores para `D_Produtos`.
5. Criei IDs para relacionar os registros da tabela fato às dimensões.
6. Criei `D_Calendario` com DAX e conectei a data à tabela `F_Vendas`.
7. Revisei os relacionamentos para manter as dimensões ligadas diretamente à fato.

## Funções DAX utilizadas

A dimensão de datas foi criada com `CALENDAR`, usando a menor e a maior data da tabela fato. `ADDCOLUMNS` acrescentou atributos de calendário, como ano, mês e trimestre. Também foram utilizadas as funções `MIN`, `MAX`, `YEAR`, `MONTH` e `FORMAT`.

```DAX
D_Calendario =
ADDCOLUMNS(
    CALENDAR(
        MIN(F_Vendas[Date]),
        MAX(F_Vendas[Date])
    ),
    "Ano", YEAR([Date]),
    "MesNumero", MONTH([Date]),
    "Mes", FORMAT([Date], "mmmm"),
    "Trimestre", "T" & FORMAT([Date], "Q"),
    "AnoMes", FORMAT([Date], "yyyy-MM")
)
```

## Medidas

```DAX
Vendas Totais =
SUM(F_Vendas[Sales])
```

```DAX
Lucro Total =
SUM(F_Vendas[Profit])
```

```DAX
Unidades Vendidas =
SUM(F_Vendas[Units Sold])
```

```DAX
Descontos Totais =
SUM(F_Vendas[Discounts])
```

```DAX
Margem de Lucro =
DIVIDE([Lucro Total], [Vendas Totais], 0)
```

A medida de margem compara o lucro total com as vendas totais e retorna o resultado como porcentagem.

## Observações

A tabela contém dados de exemplo. Portanto, o modelo serve para estudo e demonstração das etapas de modelagem, e os resultados não representam uma empresa real.

A imagem do esquema estrela e o arquivo `.pbix` devem acompanhar este README na entrega do projeto.

## Fonte dos dados

[Financial Sample para Power BI — Microsoft Learn](https://learn.microsoft.com/pt-br/power-bi/create-reports/sample-financial-download)
