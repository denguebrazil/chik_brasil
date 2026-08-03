# Projeto: Análise Epidemiológica de Chikungunya no Brasil

Olá! Seja bem-vindo(a) ao repositório do projeto de **Análise de Dados de Casos Notificados e Prováveis de Chikungunya**. Como epidemiologista e analista de dados, estruturei este projeto com um objetivo claro: transformar dados brutos de saúde pública em informações ágeis, visuais e prontas para auxiliar na tomada de decisão.

Este projeto não é voltado apenas para programadores ou cientistas de dados. Se você é gestor de saúde, enfermeiro(a), médico(a) ou pesquisador(a), os resultados gerados aqui (gráficos, mapas espaciais e relatórios em PDF) foram pensados para serem consumidos de forma clara e didática, otimizando o monitoramento da doença no nível municipal, estadual e federal.

---

## O que este projeto faz?

O sistema executa análises abrangentes em níveis Municipais, Estaduais e Nacional (Brasil), automatizando as seguintes tarefas essenciais:

1. **Processamento Automatizado (ETL):** Lê arquivos de banco de dados brutos (`.dbf`) extraídos do **SINAN**, converte-os automaticamente para Excel (`.xlsx`), consolida as bases e remove variáveis desnecessárias. Também processa e reestrutura planilhas do **GAL** (Gerenciador de Ambiente Laboratorial).
2. **Filtragem e Recortes Temporais:** Separa os casos totais notificados dos **casos prováveis** e confirmados. Isola as análises para um recorte das últimas 8 Semanas Epidemiológicas (SE), comparando as 4 semanas mais recentes com as 4 anteriores.
3. **Análise Espacial e Incidência:** 
   - Gera mapas coropléticos (georreferenciados) mostrando a distribuição espacial dos casos prováveis.
   - Calcula automaticamente o **Coeficiente de Incidência** (por 100 mil habitantes) cruzando os dados de casos com as estimativas populacionais do IBGE por Estado e Região.
4. **Comparativo Analítico (SINAN x GAL):** Avalia a proporção de exames realizados vs. casos positivos (GAL), compara casos confirmados por critérios laboratoriais e clínico-epidemiológicos (SINAN) e cruza as bases para validar a consistência dos dados.
5. **Relatórios Gerenciais em PDF:** Compila dezenas de gráficos, mapas e tabelas dinâmicas gerados em relatórios únicos em `.pdf` formatados (tamanho A4), prontos para serem anexados a boletins epidemiológicos oficiais.

---

## Tecnologias Utilizadas

Este projeto foi construído utilizando a linguagem de programação **Python**. As principais bibliotecas utilizadas foram:

* **Manipulação de Dados:** `pandas`, `numpy`, `dbfread` (leitura do SINAN).
* **Visualização de Dados:** `matplotlib`, `seaborn` (gráficos e tabelas visuais).
* **Geoprocessamento:** `geopandas` (para leitura de mapas `.geojson` e plotagem espacial).
* **Geração de Relatórios:** `reportlab` (criação estruturada dos PDFs analíticos).
* **Ambiente:** `Jupyter Notebook` (`.ipynb`).

---

## Como Instalar e Executar (Passo a Passo)

Siga estes passos para preparar seu computador e executar o pipeline completo:

### 1. Preparando o Ambiente
Você precisará do **Python** instalado. Recomendamos a distribuição [Anaconda](https://www.anaconda.com/download), que inclui o *Jupyter Notebook*. Extraia este repositório em uma pasta de sua preferência.

### 2. Instalando Dependências
Abra o terminal (ou *Anaconda Prompt*) e instale as bibliotecas necessárias:
```bash
pip install dbfread reportlab geopandas seaborn pandas openpyxl
```

### 3. Organizando os Dados (Importante!)
Para que o código funcione corretamente, você deve inserir os seguintes arquivos de base na **mesma pasta** do projeto:
* **Dados do SINAN:** Arquivos no formato `.dbf` com nomes iniciados em `CHIKON_` (ex: `CHIKON_2026.dbf`). O script exclui o DBF automaticamente após a conversão para poupar espaço.
* **Dados do GAL:** O arquivo `chikungunya_analise_completa.xlsx` com a aba `Chikungunya_por_Municipio`.
* **Dados de Apoio (IBGE e Mapas):** Os arquivos `municipios_brasil.xlsx` e `brazil-states.geojson` (necessários para os mapas e cruzamento de incidência).

### 4. Executando as Análises
Abra o **Jupyter Notebook**, navegue até a pasta e abra o arquivo principal de análise (ex: `chik_analise_brasil_estados.ipynb`). 
Defina as variáveis de filtro no início do código (como `data_analise`, `data_gal` e `filtro_estado`) e clique em `Run All` (Rodar Tudo). Pastas como `analises_chikungunya_Brasil` serão criadas automaticamente com todas as imagens e o PDF final!

---

## Como Contribuir

Profissionais de saúde, epidemiologistas e pessoas da área de dados são super bem-vindos(as):
1. Faça um *Fork* do projeto.
2. Crie uma *Branch* para sua modificação (`git checkout -b feature/NovaAnalise`).
3. Faça o *Commit* (`git commit -m 'Adicionando mapa por município'`).
4. Suba para o repositório (`git push origin feature/NovaAnalise`).
5. Abra um *Pull Request*.

---

## Licença e Créditos

Este projeto é de código aberto e visa fortalecer a vigilância em saúde no Brasil. 

**Algoritmo desenvolvido por Dengue Brazil.**

*A quantificação de casos se dá a partir da variável município de residência (ID_MN_RESI). Incidências calculadas baseadas nas estimativas populacionais do IBGE.*
