# Tech Challenge Fase 4 – Amazon Product Performance & Customer Insights

Dashboard em Power BI sobre o dataset **Amazon Sales** (Kaggle: `karkavelrajaj/amazon-sales-dataset`), com análise de desempenho de produtos, preços e descontos, e insights de clientes.

## Conteúdo da entrega

| Arquivo | Descrição |
|---|---|
| `*.pbix` | Dashboard Power BI (5 páginas) |
| `amazon.csv` | Base de dados original (Kaggle) |
| Vídeo | Apresentação executiva (até 7 minutos) |
| `README.md` | Este arquivo |

## Como abrir o dashboard (importante: ajustar o caminho do CSV)

O `.pbix` guarda os dados importados, então ele abre normalmente. Para **atualizar** os dados, o Power BI precisa saber onde está o `amazon.csv` na sua máquina:

1. Coloque o `amazon.csv` em qualquer pasta.
2. Abra o `.pbix` no Power BI Desktop.
3. Em **Página Inicial → Transformar dados → Editar parâmetros**.
4. No parâmetro `CaminhoArquivo`, informe o caminho completo do arquivo, por exemplo `C:\Users\seu_usuario\Downloads\amazon.csv`.
5. Clique em **OK** e depois em **Fechar e Aplicar** (ou **Atualizar**).

## Páginas do dashboard

1. **Visão Executiva:** KPIs (total de produtos, total de avaliações, nota média ponderada, % de produtos com nota 4+, desconto médio, total de categorias), produtos por categoria, segmentação por quadrante e Top 10 produtos por avaliações.
2. **Produtos e Categorias:** nota média por categoria, popularidade × satisfação, Top 10 subcategorias e resumo por categoria.
3. **Preços e Estratégia Comercial:** preços, desconto e economia médios; nota por faixa de desconto; desconto × avaliações; produtos por faixa de preço; quadrantes por faixa de desconto.
4. **Customer Insights:** temas recorrentes em produtos bem e mal avaliados, produtos em alerta e distribuição de notas.
5. **Recomendações:** ações por quadrante e mensagens-chave para a liderança.

A navegação entre páginas é feita pelo navegador de páginas no cabeçalho. As segmentações **Categoria Exibição** e **Faixa de Desconto** são sincronizadas entre as páginas 1 a 4.

## Preparação dos dados (Power Query)

- Leitura do CSV via parâmetro `CaminhoArquivo` (UTF-8, 16 colunas).
- Remoção das colunas de links (`img_link`, `product_link`).
- Conversão de preços (remoção de `₹` e vírgulas) e de `discount_percentage` (texto como "64%") para número.
- Separação da categoria hierárquica em `categoria_principal` e `subcategoria`.
- Novas colunas: `valor_desconto`, `faixa_desconto`, `faixa_preco` e `qtd_reviews_amostra`.
- Remoção de duplicidades: a tabela `Produtos` mantém uma linha por `product_id` (1.351 produtos).

## Modelo e medidas (DAX)

A tabela `_Medidas` reúne as medidas em pastas:

- **Portfólio:** Total Produtos, Total Categorias, Total de Avaliações.
- **Satisfação:** Nota Média, Nota Média Ponderada, % Produtos Nota 4+.
- **Preços e Descontos:** Desconto Médio, Preço Médio com Desconto, Preço Médio Original, Economia Média.
- **Engajamento e Quadrantes:** Mediana de Avaliações, Produtos Estrela, Alerta e Oportunidade.
- **Customer Insights:** % Menção Nota 4+ e % Menção Nota < 4.

**Quadrantes:** o limite de volume é a mediana de `rating_count`, e o de satisfação é nota 4.

| Quadrante | Volume de avaliações | Nota |
|---|---|---|
| Estrela | ≥ mediana | ≥ 4 |
| Alerta | ≥ mediana | < 4 |
| Oportunidade | < mediana | ≥ 4 |
| Baixo desempenho | < mediana | < 4 |

## Limitações

- A base tem **nota e quantidade de avaliações por produto**, não por cliente. Os insights de clientes vêm da comparação entre textos de avaliação de produtos bem e mal avaliados (palavras-chave), e devem ser lidos como **indícios**, não como prova.
- Os preços estão em rupias indianas (₹).
- Categorias com poucos produtos (por exemplo OfficeProducts e Outras) têm amostra pequena, e suas médias devem ser lidas com cautela.
