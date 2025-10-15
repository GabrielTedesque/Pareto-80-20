# 📊 Análise de Pareto 80/20 — Vendas de Hardware

## 🧠 Contexto

Este projeto foi criado como um **estudo de caso em análise de dados**, simulando uma empresa de **venda de hardware de computadores**.  
O objetivo é demonstrar, de ponta a ponta, o processo de **geração de base de dados**, **tratamento** e **análise de Pareto (Curva ABC)** dentro do **Power BI**.

---

## ⚙️ Etapas do Projeto

### 1️⃣ Geração da Base de Estudo

A base de dados foi **gerada com auxílio do ChatGPT**, a partir do seguinte prompt:

> Gere uma base de estudo de uma empresa de venda de hardware de computadores com seguinte requisitos:  
> COLUNAS: [CLIENTE], [DATA], [VALOR], [PRODUTO]  
> CLIENTES: 100 clientes distintos  
> DATA: 202201 a 202510  
> PRODUTO: hardware variados  
> FORMATO: CSV  

O resultado foi um arquivo `.csv` com **5.000 vendas simuladas** no período de 2022 a 2025, contendo as colunas:

| CLIENTE | DATA | VALOR | PRODUTO |
|----------|------|--------|----------|

---

### 2️⃣ Importação no Power BI

O arquivo CSV foi importado para o Power BI, criando a tabela `Fato_Vendas`.  
A partir dela, foi desenvolvida uma **tabela auxiliar DAX** para realizar a **classificação ABC dos clientes**, conforme o princípio de Pareto 80/20.

---

## 📈 Tabela DAX — Classificação ABC

```DAX
Clientes_ABC =
VAR Base =
    SUMMARIZE (
        Fato_Vendas,
        Fato_Vendas[CLIENTE],
        "Valor", SUM ( Fato_Vendas[VALOR] )
    )

VAR BaseOrdenada =
    ADDCOLUMNS (
        Base,
        "RankValor", RANKX ( Base, [Valor], , DESC, DENSE ),
        "ClienteNome", Fato_Vendas[CLIENTE]
    )

VAR TotalGeral =
    SUMX ( BaseOrdenada, [Valor] )

VAR ComPercentuais =
    ADDCOLUMNS (
        BaseOrdenada,
        "%Cliente", DIVIDE ( [Valor], TotalGeral, 0 ),
        "%Acum",
            DIVIDE (
                SUMX ( TOPN ( [RankValor], BaseOrdenada, [Valor], DESC, [ClienteNome], ASC ), [Valor] ),
                TotalGeral,
                0
            ),
        "Classe",
            SWITCH (
                TRUE(),
                DIVIDE (
                    SUMX ( TOPN ( [RankValor], BaseOrdenada, [Valor], DESC, [ClienteNome], ASC ), [Valor] ),
                    TotalGeral,
                    0
                ) <= 0.80, "A",
                DIVIDE (
                    SUMX ( TOPN ( [RankValor], BaseOrdenada, [Valor], DESC, [ClienteNome], ASC ), [Valor] ),
                    TotalGeral,
                    0
                ) <= 0.95, "B",
                "C"
            )
    )

RETURN
SELECTCOLUMNS (
    ComPercentuais,
    "CLIENTE",       [ClienteNome],
    "Valor_Total",   [Valor],
    "%Cliente",      [%Cliente],
    "%Acumulado",    [%Acum],
    "Classe_ABC",    [Classe],
    "Rank",          [RankValor]
)
```

---

## 📊 Visualizações Criadas

### 🔹 **Gráfico de Pareto (80/20) por Cliente**
Mostra o **valor acumulado das vendas** por cliente e a **curva de Pareto** destacando as classes **A (verde)**, **B (laranja)** e **C (vermelho)**.

![Gráfico Pareto por Cliente](<img width="1628" height="510" alt="image" src="https://github.com/user-attachments/assets/2b37c2a4-1c7b-4be3-bfa1-7841d9ad18b9" />
)

---

### 🔹 **Resumo da Classificação ABC**
Visualização consolidada mostrando a distribuição de clientes por classe:

| Classe | Percentual de Valor | Quantidade de Clientes |
|:-------:|:------------------:|:----------------------:|
| 🟩 A | 80% | 20 |
| 🟧 B | 15% | 30 |
| 🟥 C | 5% | 50 |

![Resumo ABC](./imagens/pareto_resumo.png)

---

## 💡 Conclusões

- Cerca de **20% dos clientes** (classe A) representam **80% do faturamento**.  
- A análise ajuda a **focar estratégias de retenção, fidelização e personalização** nos clientes mais lucrativos.  
- As classes **B e C** podem ser trabalhadas com **campanhas de crescimento e reativação**.

---

## 🧩 Tecnologias Utilizadas

| Ferramenta | Uso |
|-------------|-----|
| 🧠 ChatGPT | Geração de base simulada |
| 📊 Power BI | Modelagem, DAX e visualizações |
| 💾 CSV | Armazenamento dos dados |

---

## 🚀 Como Reproduzir

1. Gere a base de vendas (ou utilize o arquivo `base_hardware.csv`).
2. Importe o arquivo no Power BI Desktop.
3. Copie o código DAX acima para criar a tabela auxiliar `Clientes_ABC`.
4. Crie os visuais de barras e linhas com base nos campos `Valor_Total` e `%Acumulado`.
5. Formate os gráficos para destacar as classes A, B e C.

---

## 🧑‍💻 Autor

**Gabriel Tedesque**  
📍 Cientista de Dados | Power BI | Automação com Python  
🔗 [LinkedIn](https://www.linkedin.com/in/gabriel-tedesque/)  
📧 gabriel.tedesque@email.com  

---

## 📸 Prévia dos Resultados

![Base criada com ChatGPT](./imagens/base_chatgpt.png)
![Pareto Clientes](./imagens/pareto_clientes.png)
![Resumo ABC](./imagens/pareto_resumo.png)

---

📁 **Repositório criado para fins educacionais** — demonstrando integração entre **IA + Análise de Dados + Power BI**.
