# Chikungunya no Brasil: análise para a vigilância epidemiológica

Notebook em Python para apoiar o acompanhamento da chikungunya no Brasil, com análises nacionais, estaduais, municipais e por região de saúde. Destina-se a epidemiologistas e analistas/cientistas de dados que precisam transformar registros de vigilância em informações para investigação, priorização territorial e organização da resposta.

**Arquivo principal:** [chik_analise_brasil_estados_com_mapa_calor.ipynb](chik_analise_brasil_estados_com_mapa_calor.ipynb).

A versão depositada está configurada para **2026, SE 46**, com datas de referência de **25/11/2026** e seleção estadual inicial de **São Paulo (código IBGE 35)**. O notebook foi publicado sem as saídas de execuções anteriores; o código foi preservado. As bases de dados não acompanham o repositório.

## O que é analisado e por quê

| Análise | Contribuição para a vigilância |
| --- | --- |
| Evolução semanal de casos prováveis e incidência; comparação entre 2025 e 2026 | Identifica mudanças de magnitude e de distribuição temporal. A comparação em semanas equivalentes reduz distorções decorrentes de períodos de observação diferentes. |
| Diagrama de controle robusto, com histórico de 2016–2025 | Compara a incidência observada com o padrão histórico semanal e sinaliza excessos que merecem investigação. Mediana e desvio absoluto mediano (MAD) reduzem a influência de valores extremos. |
| Classificação final e critérios de confirmação | Descreve confirmados, descartados e registros em investigação, além da confirmação laboratorial e clínico-epidemiológica. Apoia o acompanhamento da investigação e do encerramento dos casos. |
| Mapas de casos e incidência por município, UF e região de saúde | Localiza concentração de notificações e diferenças na ocorrência relativa à população, contribuindo para direcionar investigação, assistência e controle vetorial. |
| Mapa de calor | Resume a concentração espacial dos casos por suavização dos totais municipais. Complementa os mapas por território na identificação visual de áreas de concentração. |
| Comparação SE 1–26 versus SE 27–semana final, rankings e janelas recentes de oito e quatro semanas | Evidencia mudanças territoriais recentes que podem ficar diluídas no acumulado anual. Regiões de saúde permitem discutir respostas articuladas entre municípios. |
| Exames realizados, resultados positivos e positividade do GAL; comparação com SINAN | Ajuda a interpretar a circulação viral em conjunto com o volume de testagem e a identificar diferenças entre os registros epidemiológicos e laboratoriais. Inclui comparações com dengue. |
| Idade, sexo, sinais, sintomas e comorbidades | Caracteriza os casos registrados e subsidia a organização da atenção e da comunicação em saúde. Frequências entre casos não medem, isoladamente, risco na população. |
| Hospitalizações, completude do campo e distribuição por idade e sexo | Acompanha a demanda assistencial e contextualiza a interpretação das hospitalizações pela qualidade do preenchimento. |
| Recorte de pessoas registradas como indígenas | Dá visibilidade à ocorrência nesse grupo e à sua distribuição territorial, apoiando a articulação com a vigilância e a atenção à saúde indígena. |
| Síntese automática, gráficos, tabelas e relatórios | Facilita a atualização periódica e a comunicação dos achados à gestão, com revisão epidemiológica antes da divulgação. |

## Definições e interpretação

- **Casos prováveis:** o processamento exclui os registros com `CLASSI_FIN = 5` (descartados). Confirmados são identificados por `CLASSI_FIN = 13`; os critérios laboratorial e clínico-epidemiológico utilizam `CRITERIO = 1` e `2`. Verifique a codificação e o universo da extração utilizada.
- **Tempo e território:** no SINAN, `SEM_PRI` representa ano/semana de início dos sintomas e origina `SE`; `DT_SIN_PRI` também é utilizada em filtros. O município de residência é identificado por `ID_MN_RESI`. Diversos blocos estaduais utilizam `SG_UF`; confira sua correspondência com a residência na base de entrada.
- **Incidência:** casos divididos pela população, multiplicados por 100.000. O notebook combina arquivos populacionais municipais/estaduais de 2025 e de regiões de saúde de 2026, além de denominadores nacionais anuais. Verifique fonte, ano e compatibilidade territorial antes de comparar resultados.
- **Diagrama de controle:** calcula mediana semanal e `sigma robusto = 1,4826 × MAD`; os limiares são mediana + 2 sigma (alerta) e mediana + 3 sigma (superior). A linha de base utiliza SE 1–52. Esses limites são sinais analíticos e exigem contextualização; esta versão não aplica nowcasting.
- **Positividade:** resultados positivos divididos pelos exames realizados, multiplicados por 100. Não equivale à incidência. Os agregados do GAL não são vinculados individualmente ao SINAN; resultados positivos não devem ser automaticamente interpretados como pessoas únicas ou somados aos casos do SINAN. Confirme o evento temporal usado na preparação das semanas do GAL.
- **Comparações:** SE 1–26 e SE 27–semana final têm durações diferentes; seus totais e incidências acumuladas precisam ser interpretados considerando o tempo de observação. Atrasos de notificação, revisão de classificação, acesso à testagem e campos ausentes também afetam os resultados. Ausência de registro não comprova ausência de transmissão.

**Atenção ao mapa de calor desta versão:** embora o título indique SE 1–semana de análise, a célula recebe `temporada_2026`, previamente filtrada para SE ≥ 27. Assim, o recorte efetivo começa na SE 27. Confira e harmonize o recorte e o título antes de usar a figura. A superfície usa pontos representativos dos municípios, ponderados por casos, grade de aproximadamente 3 km e suavização gaussiana de 30 km em EPSG:5880. Não representa localização de pacientes, local de infecção ou risco ajustado pela população.

## Dados necessários

Organize as pastas `BASES_DADOS`, `BASE_IBGE` e a pasta do notebook no mesmo diretório pai. O código usa `Path.cwd().parent` para localizar as bases; por isso, inicie a sessão na pasta que contém o notebook ou ajuste os caminhos.

| Pasta | Entradas esperadas |
| --- | --- |
| `BASES_DADOS` | Arquivos SINAN `CHIKON_*.dbf` ou `CHIKON_*.csv`; arquivos processados `chik_brasil_notificados_2026.csv` e `chik_brasil_provaveis_2026.csv` quando a conversão/consolidação é pulada. |
| `BASES_DADOS` | `CHIK_2025.csv` e `sinan_chik_semanas_2015_2025.csv` (este com `ano`, `semana_epidemiologica`, `casos`). |
| `BASES_DADOS` | `chikungunya_analise_completa.xlsx`, aba `Chikungunya_por_Municipio`, e `dengue_analise_completa.xlsx`, aba `Dengue_por_Municipio`. Os arquivos GAL devem conter `paciente_municipio_ibge`, `paciente_municipio` e colunas semanais com padrão como `SE1_exames` e `SE1_positivos`. |
| `BASES_DADOS` | Entradas SINAN de dengue `DENGON_*.dbf`/`.csv` ou os consolidados `dengue_brasil_notificados_2026.csv` e `dengue_brasil_provaveis_2026.csv`, necessários aos blocos comparativos. |
| `BASE_IBGE` | `IBGE_Municipios_Populacao_2025.csv`, `IBGE_UF_Populacao_2025.csv`, `IBGE_Municipios_Regioes_Saude_Populacao_2026.csv` e `vinculo_municipio_regiao.csv`. Preserve os nomes de colunas esperados, como `COD_IBGE`, `UF`, `Municipio`, `REGIAO_SAUDE` e `POPULACAO`, conforme cada leitura. |
| `BASE_IBGE` | `Regioes_Saude_Brasil.gpkg`, com camadas `regioes_saude` e `limites_uf`; para o mapa de calor, `BR_Municipios_2025.zip` ou shapefile completo, incluindo arquivos auxiliares. Outros mapas consultam o `geobr` pela internet. |

Os dados clínicos e demográficos precisam manter as variáveis utilizadas nos respectivos blocos, incluindo `NU_IDADE_N`, `CS_SEXO`, `CS_RACA` e `HOSPITALIZ`. Confira também separadores e codificação: as leituras incluem CSV com vírgula, ponto e vírgula e tabulação, em UTF-8 ou Latin-1.

## Como executar

1. Baixe ou clone o repositório e prepare as bases na estrutura descrita acima. Trabalhe com cópias: **as células de conversão removem os arquivos DBF após a conversão bem-sucedida**.
2. Use **Python 3.12 ou superior**; há f-strings que dependem dessa sintaxe. Em um ambiente virtual, instale as dependências no mesmo interpretador utilizado pelo kernel:

   ```bash
   python -m pip install jupyterlab ipykernel pandas numpy matplotlib seaborn dbfread geopandas geobr reportlab openpyxl pillow urllib3 scipy "shapely>=2"
   python -m jupyter lab
   ```

3. Abra o notebook e ajuste `data_analise`, `data_gal` e `se_analise` na preparação inicial. Na seção estadual, ajuste `filtro_estado` pelo código IBGE da UF.
4. Confira também anos fixos, nomes de arquivos, denominadores, recortes e limites máximos de semana em cada bloco. Alterar apenas `se_analise` não atualiza automaticamente todos os filtros; algumas janelas usam a maior SE disponível nas bases. Para outro ano, revise todas as referências a 2025/2026.
5. Execute as células em ordem, da preparação às análises nacionais e depois estaduais. As funções nacionais são reutilizadas nos blocos estaduais; após reiniciar o kernel, execute novamente suas definições para evitar erros como `NameError`.
6. Revise totais, vínculos municipais, populações, cobertura temporal e sínteses antes de compartilhar os produtos. Os arquivos são gravados em pastas como `analises_chikungunya_brasil_SE46` e `analises_chikungunya_São Paulo_SE46`, conforme o bloco; algumas exportações estão comentadas.

O mapa de calor desta versão usa SciPy e Shapely e **não requer Rasterio**. As dependências acima são uma lista derivada dos imports, sem versões integralmente fixadas. A sintaxe das células foi verificada; a execução completa depende das bases externas e não foi validada para esta publicação.

## Uso e atualização

Mantenha as bases individuais e resultados que possam identificar pessoas fora do repositório público. Registre a data de extração, a SE de referência, as fontes populacionais e eventuais ajustes a cada atualização, para permitir a reprodução e a comparação dos resultados.

**Desenvolvimento:** Dengue Brazil. As análises apoiam a tomada de decisão e devem ser interpretadas em conjunto com a investigação epidemiológica e o contexto local.
