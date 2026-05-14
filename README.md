# SADPat — Sistema Auxiliar de Desfazimento Patrimonial

Aplicativo Streamlit para cruzamento do **Relatório Sintético Patrimonial de Saídas** com o **Relatório de Depreciação Acumulada**, gerando relatório auxiliar para registro de baixas/desfazimento de bens móveis, com saídas em Excel e PDF.

## Versão 1.0

- Código organizado em camadas de leitura, tratamento, apuração, análise de consistência, exibição e exportação.
- Cache de leitura dos PDFs para reduzir reprocessamentos em reruns do Streamlit.
- Resultados preservados na tela após a geração dos arquivos Excel e PDF.
- Limpeza automática dos resultados somente quando os PDFs carregados são removidos ou substituídos.
- Layout institucional, com cabeçalho mais equilibrado, cards de indicadores, abas e botões padronizados.
- Aba **Análise de Consistência** no aplicativo e no Excel, com verificações de duplicidade, mapeamento PCASP, descrições contaminadas por valores e coerência aritmética da apuração.
- Relatório Sintético padronizado com linha de total, valores no padrão brasileiro e descrição do grupo em letras maiúsculas.
- Exportação Excel com abas: **Relatório Sintético**, **Registro SIAFI Web**, **Memória de Cálculo**, **Análise de Consistência**, **Logs** e **Metadados**.
- Aba **Registro SIAFI Web** com segregação das saídas por doação na situação **IMB037** e dedução correspondente em **IMB025**.
- Exportação PDF com cabeçalho e rodapé institucionais, tabela compatível com o layout dos relatórios de entrada e rodapé da fonte SICPAT.

## Estrutura do projeto

```text
.
├── app.py
├── modelo_grupo_x_pcasp.xlsx
├── proplan_ufmg.jpg
├── requirements.txt
└── src
    ├── models.py
    ├── parsers.py
    ├── quality.py
    ├── reporting.py
    └── utils.py
```

## Como executar localmente

```bash
pip install -r requirements.txt
streamlit run app.py
```

## Entradas esperadas

Enviar dois arquivos PDF da mesma competência:

1. Relatório Sintético Patrimonial de Saídas.
2. Relatório de Depreciação Acumulada.

A base `modelo_grupo_x_pcasp.xlsx` deve permanecer na raiz do projeto para permitir o relacionamento entre grupo patrimonial e conta contábil PCASP.

## Saídas geradas

- Arquivo Excel completo, com relatório final e abas auxiliares.
- PDF do Relatório Sintético.
- Visualização no app do Relatório Sintético, Registro SIAFI Web, Análise de Consistência, Memória de Cálculo, Logs e Metadados.

## Validação com PDFs reais - UG 153261 / abr-2026

Esta versão foi ajustada e validada com os arquivos reais:

- `153261_RMB_ABRIL_2026_UFMG.pdf`
- `153261_Depreciacao_acumulada_ABR_2026(1).pdf`

A validação confirmou a leitura da competência `abr/2026`, UG `153261 - HOSPITAL DAS CLÍNICAS`, e a compatibilização do total de saídas do Relatório Sintético Patrimonial com o total apurado no Relatório Sintético: R$ 1.468.278,10.

Ajuste técnico relevante: o parser do Relatório Sintético Patrimonial passou a priorizar o quadro sintético geral, ignorando os quadros auxiliares de entradas e saídas presentes no mesmo PDF. Esse ajuste evita dupla contagem e corrige a perda da parcela de transferências de saídas, especialmente observada no grupo 8.

## Regra de apuração v1.0 — saídas por transferência

O aplicativo utiliza o quadro **Relatório Sintético Patrimonial de Saídas** para considerar apenas a coluna **SAÍDAS**, desconsiderando a coluna **TRANSFERE SAÍDAS**. Essa regra evita duplicidade informacional, pois o SICPAT possui relatório próprio de transferências com o valor dos bens transferidos e a depreciação acumulada associada.

No processamento, o aplicativo mantém a rastreabilidade do total original do SICPAT, registra em logs o montante de transferências desconsiderado e cruza a base das demais saídas com a linha **SAÍDAS (BAIXAS)** do Relatório de Depreciação Acumulada.


