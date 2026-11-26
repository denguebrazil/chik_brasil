# Chikungunya in Brazil: analysis for epidemiological surveillance

[Português](README.md) | English

A Python notebook supporting chikungunya surveillance in Brazil through national, state, municipal and health-region analyses. It is intended for epidemiologists and data analysts/scientists who need to turn surveillance records into information for investigation, geographic prioritization and response planning.

**Main file:** [chik_analise_brasil_estados_com_mapa_calor.ipynb](chik_analise_brasil_estados_com_mapa_calor.ipynb).

This version is configured for **2026, epidemiological week (EW) 46**, with reference dates of **November 25, 2026**, and **São Paulo (IBGE code 35)** as the initial state selection. Previous execution outputs have been removed from the published notebook; the code has been preserved. Input datasets are not included.

Throughout the code, `SE` is the Portuguese abbreviation for epidemiological week, `UF` denotes a Brazilian state or the Federal District, SINAN is Brazil's notifiable diseases information system, GAL is the laboratory management system, and IBGE is the Brazilian Institute of Geography and Statistics. Original filenames, column names and variable names are retained for reproducibility.

## Analyses and their surveillance purpose

| Analysis | Contribution to surveillance |
| --- | --- |
| Weekly probable cases and incidence; comparison between 2025 and 2026 | Identifies changes in magnitude and timing. Comparing equivalent weeks reduces distortions caused by different observation periods. |
| Robust control chart using a 2016–2025 baseline | Compares observed incidence with historical weekly patterns and flags excesses requiring investigation. The median and median absolute deviation (MAD) reduce the influence of extreme values. |
| Final case classification and confirmation criteria | Describes confirmed, discarded and under-investigation records, including laboratory and clinical-epidemiological confirmation. Supports monitoring of case investigation and closure. |
| Maps of case counts and incidence by municipality, state and health region | Locates concentrations of reported cases and differences in population-adjusted occurrence, helping direct investigation, healthcare and vector control. |
| Heat map | Summarizes spatial case concentration by smoothing municipal totals. Complements administrative-area maps in the visual identification of concentrations. |
| EW 1–26 versus EW 27–final week, rankings, and recent eight- and four-week windows | Highlights recent geographic changes that may be obscured in annual totals. Health regions support coordinated responses across municipalities. |
| GAL tests performed, positive results and positivity; comparisons with SINAN | Helps interpret viral circulation alongside testing volume and identify differences between epidemiological and laboratory records. Includes comparisons with dengue. |
| Age, sex, signs, symptoms and comorbidities | Characterizes recorded cases and informs healthcare planning and health communication. Frequencies among cases alone do not measure population risk. |
| Hospitalizations, field completeness, and age and sex distributions | Monitors healthcare demand and contextualizes hospitalization findings through reporting completeness. |
| People recorded as Indigenous | Highlights occurrence and geographic distribution in this group, supporting coordination with Indigenous health surveillance and healthcare services. |
| Automated summaries, charts, tables and reports | Facilitates regular updates and communication to decision-makers, subject to epidemiological review before release. |

## Definitions and interpretation

- **Probable cases:** processing excludes records with `CLASSI_FIN = 5` (discarded). Confirmed cases are identified by `CLASSI_FIN = 13`; laboratory and clinical-epidemiological criteria use `CRITERIO = 1` and `2`, respectively. Check coding and the population covered by the input extract.
- **Time and geography:** in SINAN, `SEM_PRI` represents the year/week of symptom onset and supplies `SE`; `DT_SIN_PRI` is also used in filters. `ID_MN_RESI` identifies the municipality of residence. Several state blocks use `SG_UF`; verify its correspondence with residence in the input data.
- **Incidence:** cases divided by population, multiplied by 100,000. The notebook combines municipal/state population files for 2025 and health-region files for 2026, alongside annual national denominators. Check sources, years and geographic compatibility before comparing results.
- **Control chart:** calculates the weekly median and `robust sigma = 1.4826 × MAD`; thresholds are median + 2 sigma (alert) and median + 3 sigma (upper). The baseline uses EW 1–52. These thresholds are analytical signals requiring contextual interpretation; this version does not apply nowcasting.
- **Positivity:** positive results divided by tests performed, multiplied by 100. This is not incidence. GAL aggregates are not individually linked to SINAN; positive results should not automatically be treated as unique individuals or added to SINAN cases. Confirm the time event used to assign GAL weeks.
- **Comparisons:** EW 1–26 and EW 27–final week have different durations; totals and cumulative incidence must be interpreted in light of observation time. Reporting delays, classification revisions, access to testing and missing fields also affect findings. An absence of records does not establish an absence of transmission.

**Heat-map caveat in this version:** although the title indicates EW 1–analysis week, the cell receives `temporada_2026`, which has already been filtered to EW ≥ 27. Its effective period therefore starts at EW 27. Align the period and title before using the figure. The surface uses case-weighted representative municipal points, an approximately 3 km grid and 30 km Gaussian smoothing in EPSG:5880. It does not represent patient locations, places of infection or population-adjusted risk.

## Required data

Place `BASES_DADOS`, `BASE_IBGE` and the notebook folder under the same parent directory. The code uses `Path.cwd().parent` to locate datasets; start the session in the notebook folder or adjust the paths.

| Folder | Expected inputs |
| --- | --- |
| `BASES_DADOS` | SINAN `CHIKON_*.dbf` or `CHIKON_*.csv` files; processed `chik_brasil_notificados_2026.csv` and `chik_brasil_provaveis_2026.csv` files when conversion/consolidation is skipped. |
| `BASES_DADOS` | `CHIK_2025.csv` and `sinan_chik_semanas_2015_2025.csv` (the latter containing `ano`, `semana_epidemiologica`, `casos`). |
| `BASES_DADOS` | `chikungunya_analise_completa.xlsx`, sheet `Chikungunya_por_Municipio`, and `dengue_analise_completa.xlsx`, sheet `Dengue_por_Municipio`. GAL files must contain `paciente_municipio_ibge`, `paciente_municipio` and weekly columns following a pattern such as `SE1_exames` and `SE1_positivos`. |
| `BASES_DADOS` | Dengue SINAN inputs `DENGON_*.dbf`/`.csv` or consolidated `dengue_brasil_notificados_2026.csv` and `dengue_brasil_provaveis_2026.csv`, required for comparative blocks. |
| `BASE_IBGE` | `IBGE_Municipios_Populacao_2025.csv`, `IBGE_UF_Populacao_2025.csv`, `IBGE_Municipios_Regioes_Saude_Populacao_2026.csv` and `vinculo_municipio_regiao.csv`. Preserve expected column names such as `COD_IBGE`, `UF`, `Municipio`, `REGIAO_SAUDE` and `POPULACAO`, as required by each read operation. |
| `BASE_IBGE` | `Regioes_Saude_Brasil.gpkg`, with `regioes_saude` and `limites_uf` layers; for the heat map, `BR_Municipios_2025.zip` or the complete shapefile with its companion files. Other maps retrieve boundaries through `geobr` over the internet. |

Clinical and demographic data must retain the variables used in the relevant blocks, including `NU_IDADE_N`, `CS_SEXO`, `CS_RACA` and `HOSPITALIZ`. Also check delimiters and encoding: read operations include comma-, semicolon- and tab-separated files, using UTF-8 or Latin-1.

## Running the notebook

1. Download or clone the repository and prepare the datasets in the structure described above. Work with copies: **conversion cells delete DBF files after successful conversion**.
2. Use **Python 3.12 or later**; some f-strings require its syntax. In a virtual environment, install dependencies using the same interpreter as the notebook kernel:

   ```bash
   python -m pip install jupyterlab ipykernel pandas numpy matplotlib seaborn dbfread geopandas geobr reportlab openpyxl pillow urllib3 scipy "shapely>=2"
   python -m jupyter lab
   ```

3. Open the notebook and adjust `data_analise`, `data_gal` and `se_analise` in the initial setup. In the state section, set `filtro_estado` to the desired state's IBGE code.
4. Review hard-coded years, filenames, denominators, periods and maximum-week filters in each block. Changing `se_analise` alone does not update every filter; some windows use the highest EW available in the datasets. For a different year, review all references to 2025/2026.
5. Run cells in order: preparation, national analyses, then state analyses. National functions are reused in state blocks; after restarting the kernel, rerun their definitions to avoid errors such as `NameError`.
6. Review totals, municipal links, populations, time coverage and summaries before sharing outputs. Files are saved in folders such as `analises_chikungunya_brasil_SE46` and `analises_chikungunya_São Paulo_SE46`, depending on the block; some export statements are commented out.

The heat map in this version uses SciPy and Shapely and **does not require Rasterio**. The dependency list above was derived from imports and is not fully version-pinned. Cell syntax was checked; full execution depends on external datasets and was not validated for this publication.

## Use and updates

Keep individual-level datasets and potentially identifying outputs outside the public repository. Record the extraction date, reference EW, population sources and any adjustments for each update to support reproducibility and comparisons.

**Development:** Dengue Brazil. These analyses support decision-making and should be interpreted alongside epidemiological investigation and local context.
