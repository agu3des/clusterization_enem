# Analise de Perfis Socioeconomicos - ENEM 2024 Paraiba

## Visao Geral

Este projeto realiza uma analise exploratoria e clusterizacao dos **perfis socioeconomicos dos participantes do ENEM 2024 no estado da Paraiba**, utilizando tecnicas de aprendizado de maquina nao supervisionado para identificar grupos de candidatos com caracteristicas similares.

## Objetivos

- Compreender quem sao os estudantes que realizaram o ENEM 2024 na Paraiba
- Identificar grupos de candidatos com caracteristicas socioeconomicas semelhantes
- Analisar fatores como renda familiar, escolaridade dos responsaveis, condicoes de moradia e acesso a recursos educacionais
- Gerar insights para politicas publicas educacionais

## Estrutura do Projeto

O projeto esta dividido em **tres etapas sequenciais**:

### 1. Primeira Etapa: Pre-Tratamento dos Dados
**Arquivo:** `primeira_etapa.ipynb`

**Atividades realizadas:**
- Carregamento dos microdados do ENEM 2024 (fonte: INEP)
- Filtragem dos dados da Paraiba (128.546 participantes)
- Renomeacao de colunas para melhor compreensao
- Tratamento de valores ausentes
- Selecao de variaveis relevantes para analise socioeconomica

### 2. Segunda Etapa: Transformacao dos Dados
**Arquivo:** `segunda_etapa.ipynb`
**Output:** `dados_transformados_clusterizacao_v2.csv`

**Atividades realizadas:**
- Selecao de 19 variaveis relevantes para clusterizacao
- Pipeline de transformacao com scikit-learn:
  - **Ordinais (escolaridade):** OrdinalEncoder + StandardScaler
  - **Ordinais (bens):** OrdinalEncoder + StandardScaler
  - **Numericas:** StandardScaler + SimpleImputer (mediana)
  - **Binarias:** SimpleImputer (moda) + StandardScaler
  - **Categoricas:** OneHotEncoder (drop first) + StandardScaler
- **Padronizacao completa:** Todas as variaveis (incluindo one-hot encoded) foram padronizadas com StandardScaler
- Geracao de 35 features transformadas

### 3. Terceira Etapa: Clusterizacao e Analise de Perfis
**Arquivo:** `terceira_etapa.ipynb`
**Output:** `dados_com_clusters_e_perfis.csv`

**Atividades realizadas:**
- Aplicacao do DBSCAN com analise de parametros (eps, min_samples)
- Comparativo DBSCAN com e sem PCA (5 a 35 componentes)
- Aplicacao do K-Means com metodo do cotovelo e silhouette analysis
- Comparacao de metricas entre algoritmos
- Identificacao e nomeacao de 4 perfis socioeconomicos
- Visualizacoes e analise cruzada com variaveis categoricas

---

## Impacto da Padronizacao nos Resultados

### O Problema Inicial

Na primeira versao da segunda etapa, apenas as variaveis numericas continuas foram padronizadas. As variaveis one-hot encoded (0/1) permaneceram sem padronizacao, o que causou:

- **DBSCAN encontrava apenas 2 clusters** (basicamente dividindo por uma variavel dominante)
- As variaveis continuas (renda, escolaridade) dominavam o calculo de distancia
- As variaveis categoricas tinham pouco peso na clusterizacao

### A Solucao

Apos a **padronizacao completa de todas as variaveis** (incluindo one-hot encoded), os resultados mudaram drasticamente:

| Metrica | Sem Padronizacao Completa | Com Padronizacao Completa |
|---------|---------------------------|---------------------------|
| DBSCAN (clusters) | 2 | **166** |
| DBSCAN + PCA 5 (clusters) | - | **4** |
| Estrutura dos dados | Aparentemente binaria | **Multi-cluster** |

### Por que isso acontece?

- Variaveis one-hot (0/1) tem variancia muito menor que variaveis continuas
- Sem padronizacao, o algoritmo "ignora" as categoricas no calculo de distancia
- Com padronizacao, todas as variaveis tem peso equivalente (media=0, desvio=1)
- Isso permite identificar subgrupos baseados em combinacoes de todas as caracteristicas

---

## Resultados Finais

### Metricas de Clusterizacao

| Algoritmo | Clusters | Silhouette | Calinski-Harabasz | Davies-Bouldin |
|-----------|----------|------------|-------------------|----------------|
| DBSCAN (PCA 5) | 4 | **0.2901** | - | - |
| DBSCAN (PCA 15) | 68 | 0.2162 | 2226 | 1.13 |
| DBSCAN (original) | 166 | 0.1157 | 1326 | 1.49 |
| K-Means (k=13) | 13 | 0.1527 | 8606 | 1.44 |
| K-Means (k=4) | 4 | 0.1371 | - | - |

**Escolha final:** K-Means com k=4 para melhor interpretabilidade, alinhado com o DBSCAN+PCA5.

### Perfis Socioeconomicos Identificados

| Perfil | N | % | Caracteristicas Principais |
|--------|---|---|---------------------------|
| **Baixa Renda com Capital Cultural** | 20.197 | 15.7% | Menor renda, porem pais com escolaridade na media. Alta proporcao de treineiros. |
| **Vulnerabilidade Socioeconomica** | 76.299 | 59.4% | Maior grupo. Renda e escolaridade dos pais abaixo da media, menor indice de bens. |
| **Classe Media Emergente** | 11.239 | 8.7% | Renda acima da media, pais com baixa escolaridade. Mobilidade social ascendente. |
| **Classe Alta** | 20.811 | 16.2% | Maior renda, pais com alta escolaridade, maior acesso a tecnologia e escola privada. |

### Principais Achados

1. **Vulnerabilidade e a maioria:** 59.4% dos participantes estao em situacao de vulnerabilidade socioeconomica, com deficit tanto economico quanto de capital cultural.

2. **Capital cultural vs renda:** O perfil "Baixa Renda com Capital Cultural" revela que escolaridade dos pais e renda nao estao perfeitamente correlacionados.

3. **Mobilidade social:** A "Classe Media Emergente" (8.7%) representa familias onde os filhos estao superando o nivel educacional dos pais.

4. **Treineiros como estrategia:** O perfil "Baixa Renda com Capital Cultural" tem a maior proporcao de treineiros (z=+1.98), indicando que pais mais escolarizados incentivam a pratica do ENEM.

5. **Desigualdade no acesso:** A "Classe Alta" tem amplo acesso a tecnologia e maior presenca em escolas privadas.

---

## Tecnologias Utilizadas

- **Python 3.x**
- **Pandas** - Manipulacao de dados
- **NumPy** - Operacoes numericas
- **Scikit-learn** - Algoritmos de ML e pre-processamento
- **Matplotlib/Seaborn** - Visualizacoes
- **Jupyter Notebook** - Ambiente de desenvolvimento

## Metodologia

### Variaveis Utilizadas

| Tipo | Variaveis | Transformacao |
|------|-----------|---------------|
| Ordinais | `esc_mae`, `esc_pai`, `tem_computador_notebook`, `tem_carro` | OrdinalEncoder + StandardScaler |
| Numericas | `renda_log`, `pessoas_residencia`, `total_bens`, `ano_conclusao` | StandardScaler |
| Binarias | `tem_internet_wifi`, `possui_renda`, `treineiro` | Imputer (moda) + StandardScaler |
| Categoricas | `faixa_etaria`, `sexo`, `estado_civil`, `cor_raca`, `nacionalidade`, `status_conclusao`, `tipo_ensino`, `tipo_escola` | OneHotEncoder + StandardScaler |

### Algoritmos de Clusterizacao

**DBSCAN:**
- Testados eps de 0.5 a 4.0 e min_samples de 5 a 20
- Melhor configuracao: eps=2.5, min_samples=10
- Comparativo com PCA: melhor silhouette com 5 componentes (0.2901)
- Encontrou 166 clusters nos dados originais, 4 clusters com PCA 5

**K-Means:**
- Testados k de 2 a 14
- Melhor k pelo silhouette: k=13 (0.1527)
- Escolhido k=4 para maior interpretabilidade e alinhamento com DBSCAN+PCA5

## Como Executar

### Pre-requisitos
```bash
pip install pandas numpy matplotlib seaborn scikit-learn jupyter
```

### Execucao
1. **Obter os microdados do ENEM 2024:**
   - Acesse: https://www.gov.br/inep/pt-br/acesso-a-informacao/dados-abertos/microdados/enem
   - Baixe e extraia o arquivo de microdados

2. **Executar os notebooks em ordem:**
   ```bash
   jupyter notebook primeira_etapa.ipynb
   jupyter notebook segunda_etapa.ipynb
   jupyter notebook terceira_etapa.ipynb
   ```

## Arquivos do Projeto

```
clusterization_enem/
├── README.md
├── primeira_etapa.ipynb                    # Pre-tratamento dos dados
├── segunda_etapa.ipynb                     # Transformacao dos dados
├── terceira_etapa.ipynb                    # Clusterizacao e analise
├── dados_transformados_clusterizacao_v2.csv  # Dataset transformado (gerado)
└── dados_com_clusters_e_perfis.csv         # Dataset com perfis (gerado)
```

## Fonte dos Dados

**Instituto Nacional de Estudos e Pesquisas Educacionais Anisio Teixeira (INEP)**
- Link: https://www.gov.br/inep/pt-br/acesso-a-informacao/dados-abertos/microdados/enem
- Ano: 2024
- Estado: Paraiba (PB)
- Registros: 128.546 participantes

## Colaboradores

- [@araujo-angel](https://github.com/araujo-angel)
- [@agu3des](https://github.com/agu3des)
- [@l-e-t-i-c-i-a](https://github.com/l-e-t-i-c-i-a)
- [@alessandrojunior1](https://github.com/alessandrojunior1)

## Licenca

Este projeto utiliza dados publicos disponibilizados pelo INEP/MEC.

---

**Projeto de analise de dados socioeconomicos do ENEM 2024 - Paraiba**
