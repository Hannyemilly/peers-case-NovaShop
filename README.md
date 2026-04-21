# Case NovaShop — Peers Group

Análise de dados do e-commerce NovaShop para identificar padrões e anomalias que explicam a alta taxa de cancelamentos, devoluções e crescimento de tickets de suporte.

---

## Estrutura do Projeto

```
peers-case-novashop/
│
├── data/                        # Dados brutos originais (não modificar)
│   ├── clientes.csv
│   ├── pedidos.csv
│   ├── produtos.csv
│   ├── itens_pedido.csv
│   ├── avaliacoes.csv
│   └── tickets_suporte.csv
│
├── instructions/
│   └── CDPeers Group Case Digital_Novashop.pptx   # Briefing do case
│
├── notebook/
│   └── analise_novashop.ipynb   # Análise completa em Python
│
├── output/
│   ├── dados_tratados/          # CSVs limpos → fonte do Power BI
│   │   ├── clientes.csv
│   │   ├── pedidos.csv
│   │   ├── produtos.csv
│   │   ├── itens_pedido.csv     # inclui coluna valor_item calculada
│   │   ├── avaliacoes.csv
│   │   └── tickets.csv
│   ├── dados_agregados/         # Tabelas pré-calculadas de apoio
│   │   ├── agg_status_pedidos.csv
│   │   ├── agg_top_produtos.csv
│   │   ├── agg_evolucao_mensal.csv
│   │   ├── agg_canal_aquisicao.csv
│   │   └── fato_pedidos_clientes.csv
│   └── graficos/                # Visualizações geradas pelo notebook
│       ├── p1_status_pedidos.png
│       ├── p2_top10_produtos.png
│       ├── p3_ticket_b2c_b2b.png
│       ├── p4_evolucao_mensal.png
│       ├── p5_canal_aquisicao.png
│       └── extra_tickets.png
│
├── Case - Peers PBI.pbix        # Dashboard Power BI
├── requirements.txt             # Dependências Python
└── README.md
```

---

## Pré-requisitos

- Python 3.11+
- VS Code com a extensão **Python** (ms-python.python) instalada
- Power BI Desktop (para o dashboard)

---

## Instalação

**1. Clonar ou baixar o projeto**

Certifique-se de que a estrutura de pastas está igual ao diagrama acima.

**2. Criar e ativar o ambiente virtual (venv)**

É recomendado usar um ambiente virtual para isolar as dependências do projeto.

Windows:
```bash
python -m venv venv
venv\Scripts\activate
```

Linux / macOS:
```bash
python3 -m venv venv
source venv/bin/activate
```

> Após ativar, o terminal deve exibir `(venv)` no início da linha.

**3. Instalar dependências Python**

```bash
pip install -r requirements.txt
```

**4. Selecionar o interpretador Python no VS Code**

Após instalar as dependências, é necessário apontar o VS Code para o ambiente virtual criado:

1. Abra a pasta do projeto no VS Code (`File → Open Folder`)
2. Pressione `Ctrl+Shift+P` e digite **Python: Select Interpreter**
3. Selecione a opção que aponta para o `venv` local — geralmente exibida como:
   - `Python 3.x.x ('venv': venv)` — Windows: `.\venv\Scripts\python.exe`
4. Reabra qualquer arquivo `.ipynb` aberto para garantir que o kernel use o venv selecionado

> Se o venv não aparecer na lista, clique em **Enter interpreter path** e navegue manualmente até `venv\Scripts\python.exe` (Windows) ou `venv/bin/python` (Linux/macOS).

**5. Desativar o ambiente virtual (quando terminar)**

```bash
deactivate
```

---

## Como Rodar

### Notebook Python

Abra o arquivo `notebook/analise_novashop.ipynb` diretamente no VS Code.

Confirme que o kernel selecionado (canto superior direito do notebook) é o do `venv` — se não for, clique nele e troque para o interpretador configurado no passo 4.

Execute todas as células com `Run All` (botão no topo) ou `Ctrl+F5`.

O notebook vai:
1. Ler os dados brutos de `../data/`
2. Diagnosticar e tratar inconsistências
3. Responder as 6 perguntas com código, tabelas e gráficos
4. Exportar os dados tratados para `../output/`

> Os arquivos em `output/` já estão gerados. Só é necessário rodar o notebook novamente se quiser reproduzir ou alterar as análises.

### Power BI

1. Abra `Case - Peers PBI.pbix` no Power BI Desktop
2. Todas as tabelas já apontam para `output/dados_tratados/`
3. Se necessário atualizar os dados: `Página Inicial → Atualizar`

> **Atenção:** Os CSVs usam ponto (`.`) como separador decimal. O Power Query está configurado com locale `en-US` para garantir a leitura correta. Não altere o tipo das colunas decimais sem manter o parâmetro `"en-US"` no `TransformColumnTypes`.

---

## Perguntas Respondidas

| # | Pergunta | Onde |
|---|---|---|
| 1 | Volume de pedidos por status + distribuição % | Notebook + PBI pág. 1 |
| 2 | Top 10 produtos por quantidade + receita | Notebook + PBI pág. 2 |
| 3 | Ticket médio B2C vs B2B + teste estatístico | Notebook + PBI pág. 3 |
| 4 | Evolução mensal 2023–2024 + sazonalidade | Notebook + PBI pág. 4 |
| 5 | Canal de aquisição × cancelamento e ticket médio | Notebook + PBI pág. 5 |
| 6 | Inconsistências na base + tratamento documentado | Notebook + PBI pág. 6 |

---

## Modelo de Dados (Power BI)

```
clientes  (1) ──── (N)  pedidos
produtos  (1) ──── (N)  itens_pedido
pedidos   (1) ──── (N)  itens_pedido
pedidos   (1) ──── (N)  tickets
pedidos   (1) ──── (N)  avaliacoes
dCalendario (1) ── (N)  pedidos        ← tabela calculada em DAX
```

---

## Principais Achados

| # | Achado | Evidência |
|---|---|---|
| 1 | 1 em cada 4 pedidos não é entregue | 16,9% cancelados + 7,6% devolvidos = 24,5% |
| 2 | Ticket de suporte em 27% dos pedidos | 4.000 tickets para 14.921 pedidos |
| 3 | 41,3% dos tickets sem resolução | 806 abertos + 847 escalados |
| 4 | B2B gasta 6x mais por pedido que B2C | R$ 7.778 vs R$ 1.265 (p < 0,001, Cohen's d = 2,19) |
| 5 | paid_search com 30,8% de cancelamento | 2,5x acima da média dos outros canais (~12%) |
| 6 | Divergência estrutural nos dados | valor_total ≠ soma dos itens em 99,9% dos pedidos |

---

## Dependências Python

| Biblioteca | Uso |
|---|---|
| `pandas` | Manipulação e análise dos dados |
| `numpy` | Cálculos numéricos |
| `matplotlib` | Visualizações |
| `seaborn` | Estilo dos gráficos |
| `scipy` | Testes estatísticos (Mann-Whitney, Shapiro-Wilk) |"# peers-case-NovaShop" 
