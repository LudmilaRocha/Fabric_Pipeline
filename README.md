# Brasil nas Olimpíadas 🏅

> Pipeline de dados construído no Microsoft Fabric para analisar a performance do Brasil nos Jogos Olímpicos — 1896 a 2024.

## Sobre o projeto

A partir de um dataset com atletas olímpicos de todo o mundo, filtramos e segmentamos os dados brasileiros para responder perguntas estratégicas sobre medalhas, esportes e atletas ao longo de 128 anos de história olímpica.

## Perguntas que este projeto responde

| # | Dimensão | Pergunta |
|---|----------|----------|
| 01 | Gênero | Qual gênero conquistou mais medalhas pelo Brasil? |
| 02 | Esporte | Qual esporte trouxe mais ouro, prata e bronze? |
| 03 | Ano | Em qual edição o Brasil teve seu melhor desempenho? |
| 04 | Atletas | Quem são nossos top 3 atletas com mais medalhas? |

## Arquitetura Medallion

```
CSV/Excel  →  [Bronze]  →  [Silver]  →  [Gold]  →  Power BI
               raw          limpeza      agregações
```

## Tecnologias

- Microsoft Fabric / OneLake
- PySpark + Delta Lake
- Lakehouse (Bronze · Silver · Gold)
- Data Factory (pipelines)

## Status

- [x] Bronze — ingestão do dataset
- [x] Silver — limpeza, tipos, deduplicação (8.500 registros validados)
- [x] Gold — agregações por Brasil
- [ ] Visualização Python

## Dataset

[olympics_athletes_dataset.csv](./olympics_athletes_dataset.csv) — Atletas Olímpicos 1896–2024
