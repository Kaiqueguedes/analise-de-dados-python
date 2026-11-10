

Dashboard de indicadores do mercado de trabalho brasileiro construído com dados públicos do IBGE (PNAD Contínua Trimestral), com estatística descritiva em Python.

**Indicador:** Trabalho e População Empregada — desocupação, informalidade e rendimento
**Disciplina:** Python e Matemática · Técnico em Informática / Ensino Médio
**Período:** Etapa 3 — apresentação de 26/10/2026 a 30/10/2026

---

## Sobre o indicador

Este painel responde às perguntas de negócio da Persona 6, cujo cenário é o combate à informalidade e a geração de emprego formal, com atenção à paridade salarial.

| Persona | Papel |
| :--- | :--- |
| Márcia | Ministra do Trabalho |
| Renato | Conselho de Indústrias |
| Sílvia | Sebrae |

Perguntas de negócio respondidas:

1. Qual indicador deve liderar a política de emprego — desocupação ou informalidade?
2. Qual a experiência de 2012 a 2022 por estado?
3. Há diferença entre estados e regiões?
4. Qual indicador sustenta a prioridade de política pública da startup?

---

## Fonte dos dados

Todos os dados vêm da API de Agregados do IBGE, que é a interface programática do SIDRA. Nenhum arquivo precisa ser baixado previamente.

| Tabela SIDRA | Conteúdo | Uso no painel |
| :--- | :--- | :--- |
| [4093](https://sidra.ibge.gov.br/tabela/4093) | Pessoas de 14 anos ou mais: ocupadas, desocupadas, em situação de informalidade e respectivas taxas, por sexo | Indicador principal |
| [4095](https://sidra.ibge.gov.br/tabela/4095) | Taxa de desocupação por nível de instrução | Cruzamento com escolaridade |
| [4708](https://sidra.ibge.gov.br/tabela/4708) | Taxa de informalidade das pessoas ocupadas | Indicador alternativo |

- **Pesquisa:** PNAD Contínua Trimestral
- **Periodicidade:** trimestral, de 1º trimestre de 2012 em diante
- **Abrangência:** Brasil, Grandes Regiões, Unidades da Federação, Regiões Metropolitanas e Municípios
- **Variáveis usadas:** `4099` (taxa de desocupação, %) e `12466` (taxa de informalidade, %)
- **Documentação da API:** https://servicodados.ibge.gov.br/api/docs/agregados?versao=3

---

## Como executar

```bash
git clone https://github.com/Kaiqueguedes/analise-de-dados-python.git
cd analise-de-dados-python

python -m venv .venv

# Windows
.venv\Scripts\activate
# Linux / macOS
source .venv/bin/activate

pip install -r requirements.txt
streamlit run app.py
```

O navegador abre em `http://localhost:8501`.

> **Requer internet.** Os dados são buscados ao vivo na API do IBGE. Se a conexão falhar durante a apresentação, envie um CSV/XLSX exportado do SIDRA pelo `st.file_uploader` na barra lateral — o painel usa o arquivo com prioridade.

---

## Estrutura do projeto

```
analise-de-dados-python/
├── app.py             # Interface Streamlit: filtros, KPIs, gráficos e leitura
├── ibge_api.py        # Acesso à API de Agregados do IBGE (com cache)
├── etl.py             # Limpeza, filtros e estatística descritiva
├── requirements.txt   # Dependências
└── README.md          # Este arquivo
```

| Arquivo | Responsabilidade |
| :--- | :--- |
| `ibge_api.py` | Monta as URLs da API, faz as requisições e devolve `DataFrame`. Aplica cache de 6 h, porque a PNAD é trimestral. |
| `etl.py` | Trata valores ausentes (`-`, `..`), remove duplicatas, aplica filtros e calcula as estatísticas. |
| `app.py` | Monta a interface. Expõe a função `render()`, para poder ser importada pelo painel integrado do grupo. |

---

## Funcionalidades

### Barra lateral (filtros)

- **Regiões** — agrupamento territorial (Norte, Nordeste, Centro-Oeste, Sudeste, Sul)
- **UFs** — filtro por unidade da federação, restrito às regiões escolhidas
- **Período** — faixa de anos de 2012 a 2022
- **Indicador** — alterna entre taxa de desocupação e taxa de informalidade
- **Upload** — `st.file_uploader` para CSV/XLSX exportado do SIDRA

### Cards de estatística

Média, mediana, moda, Q1, Q3, IQR, amplitude, desvio padrão, mínimo e máximo — todas as medidas exigidas no objetivo específico do projeto.

### Gráficos

| Gráfico | O que mostra |
| :--- | :--- |
| Histograma | Forma da distribuição do indicador |
| Boxplot por região | Dispersão e outliers, comparando as cinco regiões |
| Barras horizontais | Ranking das 15 UFs com maior ou menor taxa |
| Linha temporal | Evolução trimestral do indicador |
| Linha por sexo | Desocupação de homens × mulheres, por UF |

### Análise de outliers

Aplicação do critério de Tukey (limites de `Q1 − 1,5 × IQR` e `Q3 + 1,5 × IQR`), com listagem das observações atípicas e discussão explícita do efeito delas sobre a média em comparação à mediana — item cobrado no critério 2 da rubrica.

### Leitura interpretativa

Bloco final que traduz os números para cada persona do cenário: o que a mediana e a dispersão significam para a política nacional (Márcia), para o dimensionamento setorial (Renato) e para a atuação local (Sílvia).

---

## Alinhamento à BNCC

| Habilidade | Como aparece no projeto |
| :--- | :--- |
| **EM13MAT104** | Interpretação das taxas de desocupação e informalidade como índices socioeconômicos |
| **EM13MAT316** | Cálculo de média, moda, mediana, amplitude e desvio padrão |
| **EM13MAT202** | Comunicação dos resultados por meio de gráficos interativos |
| **EM13MAT406** | Construção e leitura de tabelas e gráficos de frequência com apoio de software |
| **EM13CHS402** | Comparação de indicadores de emprego e renda entre estados, regiões e períodos |
| **EM13CNT104** | Uso de tecnologias digitais e API pública para resolver um problema real |

---

## Stack

- **Python 3.11+**
- **Streamlit** — interface web
- **Pandas / NumPy** — tratamento e estatística
- **Plotly** — gráficos interativos
- **Requests** — consumo da API do IBGE
- **openpyxl** — leitura de arquivos `.xlsx` enviados pelo usuário

---

## Integração com o painel do grupo

Este projeto é a seção individual da Persona 6. Para integrar ao painel único do grupo, basta importar e chamar `render()` dentro de uma aba:

```python
import streamlit as st
from app import render as render_trabalho

abas = st.tabs(["Demografia", "Alfabetização", "Indígenas", "Mulheres", "Trabalho"])

with abas[4]:
    render_trabalho()
```

O `st.set_page_config()` está protegido dentro do bloco `if __name__ == "__main__"`, justamente para não conflitar quando o módulo é importado pelo painel integrado.

---

## Referências

- **IBGE** — SIDRA / PNAD Contínua Trimestral. https://sidra.ibge.gov.br/pesquisa/pnadct
- **API de Agregados do IBGE** — https://servicodados.ibge.gov.br/api/docs/agregados?versao=3
- **API de Localidades do IBGE** — https://servicodados.ibge.gov.br/api/docs/localidades
- **BNCC — Ensino Médio** — https://www.gov.br/mec/pt-br/cne/bncc-2013-ensino-medio
- **Documentação Streamlit** — https://docs.streamlit.io
- **Documentação Plotly** — https://plotly.com/python/
- **Documentação Pandas** — https://pandas.pydata.org/docs/
- **Documentação NumPy** — https://numpy.org/doc/
