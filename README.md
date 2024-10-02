# RDO - Relatório Diário de Operação | Energimp - Novo formato de relatório usando Power BI

# Sobre a ENERGIMP
A ENERGIMP nasce da união de esforços para promover investimentos em projetos de geração de energia elétrica a partir de fontes renováveis, como a eólica.
Fatores naturais como a climatologia e o relevo dos estados do Ceará e Santa Catarina favoreceram a construção dos complexo eólicos que hoje geram energia para diversas cidades dessas regiões.

A produção de energia limpa é imprescindível para a manutenção da sustentabilidade e para o respeito ao meio ambiente.

A empresa nasceu no ano 2000, criada pela fabricante de aerogeradores argentina IMPSA, evoluiu e hoje conta Parques Eólicos nos Estados do Ceará e Santa Catarina. 

## Índice 

* [1. Problema de Negócio](#1-problema-de-negócio)
* [2. Premissas assumidas](#2-premissas-assumidas)
* [3. Estratégia da solução](#3-estratégia-da-solução)
* [4. O produto final do projeto](#4-o-produto-final-do-projeto)
* [5. Conclusões](#5-conclusões)
* [6. Próximos passos](#6-próximos-passos)
* [7. Tecnologias e Ferramentas utilizadas](#7-tecnologias-e-ferramentas-utilizadas)
* [8. Documentação Completa do Projeto](#8-documentação-completa-do-projeto)


# 1. Problema de negócio

O Centro de Operações gera um relatório diariamente sobre todos os acontecimentos e eventos registrados no Software de O&M utilizado (Equipament - EqM) diretamente da aplicação. As informações desses eventos são de data/hora de início e fim, descrição das atividades, causas, motivos e número dos eventos e solicitações de intervenções. Esse relatório tem como destinatário todas as áreas de Operação e Manutenção da ENERGIMP, principalmente as lideranças, é uma maneira rápida de consumir os dados do dia anterior separado por Complexos Eólicos e facilita a tomada de decisão de forma ágil.

O Desafio

A área de operações sentiu a necessidade de reformular o relatório, modificando seu visual, acrescentando mais informações e adquirindo independência deste Relatório para executar as manutenções que desejar. Com isso, passa a não depender de fornecedor para melhorias e correções, ganhando bastante tempo e economizando $$$.


# 2. Premissas assumidas 

Foi utilizado uma base de dados privada da ENERGIMP.

Foram assumidas as principais visões de negócios: Complexos Eólicos, Eventos, Ocorrências, Intervenções, Aerogeradores (WTG) e Subestações e Redes(BOP).

# 3. Estratégia da solução

O relatório foi dividido em três páginas, que reflete as principais visões da operação:

📈 Subestações e Redes - BOP
- Aqui são apresentadas os eventos de ocorrências e intervenções que ocorreram nas Subestações e Redes.

📊 Aerogeradores - WTG
- Aqui são apresentadas os eventos de ocorrências e intervenções que ocorreram nos Aerogeradores.

📝 Mais Informações 
- Aqui são apresentadas os eventos de controle de potência/tensão e Solicitações de Intervenção que foram Prorrogadas e Reprogramadas, com seus respectivos motivos e comentários.

# 4. O produto final do projeto
Painel online disponível para acesso via Power BI Web (funcionários ENERGIMP);

O painel pode ser acessado através desse link: https://app.powerbi.com/links/SUepEAvuaa?ctid=e4b67d43-6dcf-4ce9-b51c-35b8363fffd3&pbi_source=linkShare&bookmarkGuid=b3a3ed61-8110-4423-97ea-7eaff8571939

O Relatório será filtrado por Complexo Eólico, exportado .PDF e enviado ao corpo técnico (O&M) da ENERGIMP.

# 5. Conclusões
A partir da implementação do painel, a área de Operações terá independência deste relatório, com um visual melhor e com mais informações que o modelo anterior desenvolvido pela empresa dona do Software de O&M.

# 6. Próximos passos
Inserir gráfico do comportamento da geração de energia nas últimas 24h, na parte superior do relatório.

# 7. Tecnologias e Ferramentas utilizadas

- ![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=Power%20BI&logoColor=black)

# 8. Documentação Completa do Projeto

# Documentação da Tabela `bi_rdo_evento`

## Estrutura da Tabela

| Coluna               | Tipo        |
|----------------------|-------------|
| cod_evento           |decimal number|
| desc_num_evento      | text        |
| desc_evento          | text        |
| data_inicio          | datetime    |
| hora_inicio          | time        |
| data_fim             | datetime    |
| hora_fim             | time        |
| desc_evento_estado   | text        |
| Tipo Evento          | text        |
| Natureza do Evento   | text        |
| Tipo Complemento     | text        |
| Causa                | text        |
| Complexo Eólico      | text        |
| Equipamento          | text        |
| Origem Resumo        | text        |
| Data/Hora Inicio     | datetime    |
| Data/Hora Fim        | datetime    |
| Unidade de Medida    | text        |

## Descrição do Processo de Transformação

1. **Fonte de Dados**: 
   - Os dados são extraídos da fonte `PowerPlatform.Dataflows` e navega-se por vários IDs para acessar a tabela correta.

2. **Adição de Colunas Condicionais (Usina e Complexo Eólico)**:
   - A coluna `Usina` é criada através de uma série de condições if baseadas na coluna cod_instalacao. Dependendo do código da instalação, uma usina específica é atribuída, classificando cada evento de acordo com sua localização.
   - A coluna `Complexo Eólico` é gerada a partir da coluna `Usina`, aplicando novamente uma série de condições que determinam a qual complexo eólico a usina pertence.

3. **Mesclagem de Colunas**:
   - A coluna `Equipamento` é criada ao combinar o código da instalação `cod_instalacao` e a descrição da localização `desc_localizacao`. Isso resulta em uma descrição mais detalhada de cada equipamento envolvido no evento.
   - A coluna `No Evento` mescla os valores `numr_evento` (número do evento) e `numr_ano` (ano do evento) em um único campo de texto, o que ajuda a identificar unicamente cada evento.

4. **Limpeza e Tratamento de Texto**:
   - O valor de `text_evento_tipo` tem alguns caracteres substituídos para uniformizar o formato de texto.
   - A coluna `text_evento_tipo` é dividida em várias novas colunas com base no delimitador ">", criando subcategorias do tipo de evento.

5. **Filtragem de Linhas**:
   - O código filtra os dados para remover linhas em que `text_evento_tipo.2` contém tipos de eventos considerados irrelevantes, como `DEFEITOS (INFRA COG-PERIFÉRICOS)`, `DEMAIS EQUIPAMENTOS`, `INFORMAÇÕES RELEVANTES` e `PASSAGEM DE TURNO`. Isso garante que a tabela resultante contenha apenas eventos críticos para a análise.

6. **Renomeação de Colunas**:
   - Diversas colunas são renomeadas para tornar seus propósitos mais claros:
     * `text_evento_tipo.2` é renomeado para `Tipo Evento`.
     * `text_evento_tipo.3` se torna `Natureza do Evento`.
     * Outras colunas são renomeadas para descrever melhor os dados que contêm, como `Causa` e `Origem`. 

7. **Criação de Colunas de Data e Hora**:
   - Novas colunas `Data/Hora Inicio` e `Data/Hora Fim` são criadas ao combinar os campos `data_inicio` e `hora_inicio`, e `data_fim` e `hora_fim`, respectivamente. Isso gera colunas `datetime` que facilitam o acompanhamento do início e fim de cada evento.

8. **Adição de Coluna de Unidade de Medida**:
   - A coluna `Unidade de Medida` é adicionada para associar uma unidade apropriada de controle de tensão (kV), potência ativa (MW) ou potência reativa (MVAr) com base no tipo de evento. Eventos sem uma dessas categorias recebem um valor em branco.

9. **Seleção de Colunas**:
   - A função `Table.SelectColumns` reduz o conjunto de colunas para aquelas consideradas relevantes para a análise final. Colunas intermediárias ou desnecessárias são removidas, mantendo apenas os campos essenciais.

# Documentação da Tabela `bi_evento_registro`

## Estrutura da Tabela

| Coluna        | Tipo   |
|---------------|--------|
| data_registro |  date  |
| cod_evento    |decimal number|

## Descrição do Processo de Transformação

1. **Fonte de Dados**: 
   - O código conecta-se à plataforma PowerPlatform Dataflows e navega por um conjunto de IDs, selecionando a entidade `bi_evento_registro` a partir de workspaces e dataflows.

2. **Filtragem de Linhas**: 
   - A transformação utiliza a função `Table.SelectRows` para filtrar os dados da tabela `bi_evento_registro_`, garantindo que apenas os registros com `data_registro` posterior a 1º de janeiro de 2023 sejam mantidos. Essa filtragem reduz o conjunto de dados a eventos mais recentes.

3. **Seleção de Colunas**: 
   - A tabela resultante é filtrada novamente para selecionar apenas duas colunas: `data_registro` e `cod_evento`. Isso torna a tabela mais eficiente ao remover colunas que não são necessárias para a análise final.
  
# Documentação da Tabela `bi_rdo_control`

## Estrutura da Tabela

| Coluna                    | Tipo   |
|---------------------------|--------|
| cod_evento                | number |
| Valor Anterior             | number |
| Limitada Para              | number |
| Valor Anterior Calculado   | number |
| Limitada Para Calculado    | number |

## Descrição do Processo de Transformação

1. **Fonte de Dados**: 
   - O código se conecta à PowerPlatform Dataflows e navega por um conjunto de IDs para acessar a tabela `bi_evento_carac`.

2. **Filtragem de Linhas**: 
   - Primeiramente, filtra os dados para incluir apenas as linhas onde a categoria (`desc_categoria`) é igual a `Controle de Potência`.
   - Em seguida, remove colunas e valores de linhas que não são relevantes, como descrições específicas de características (por exemplo, "Operador COG", "Previsão Retorno").

3. **Pivot de Colunas**: 
   - A tabela é pivotada com base na coluna `desc_carac`, utilizando as respostas como valores para criar novas colunas.

4. **Mesclagem de Colunas**: 
   - As colunas `Potência Antes`, `Potência Atual` e `Tensão Atual` são combinadas em uma nova coluna chamada `Valor Anterior`.
   - Similarmente, as colunas `Potência Limitada Para`, `Potência Solicitada` e `Tensão Solicitada` são mescladas na coluna `Limitada Para`.

5. **Conversão de Tipos**: 
   - As colunas `Valor Anterior` e `Limitada Para` são convertidas para o tipo `number` para garantir que operações matemáticas possam ser realizadas corretamente.

6. **Substituição de Valores**: 
   - Campos vazios nas colunas "Agente Solicitante", "Solicitante" e "Motivo" são substituídos por `null` para limpar o dataset.

7. **Filtragem e Remoção de Colunas**: 
   - As linhas onde o código de evento (`cod_evento`) é menor ou igual a 39025 são removidas, e colunas irrelevantes são descartadas, como "Agente Solicitante" e "Solicitante".

8. **Criação de Colunas Calculadas**: 
   - Novas colunas "Valor Anterior Calculado" e "Limitada Para Calculado" são adicionadas, dividindo os valores de "Valor Anterior" e "Limitada Para" por 1000 para obter resultados em uma escala menor.

9. **Reordenação de Colunas**: 
   - As colunas são reordenadas para uma melhor organização, com a coluna `cod_evento` sendo posicionada à frente.

10. **Conversão Final de Tipos**: 
    - As colunas calculadas são convertidas novamente para o tipo `number` para garantir que estejam no formato correto.

# Documentação da Tabela `bi_si`

## Estrutura da Tabela

| Coluna                    | Tipo   |
|---------------------------|--------|
| cod_si                    | number |
| desc_servico               | text   |
| desc_numero_si             | text   |
| data_inicio                |  date  |
| data_fim                   |  date  |
| data_inicio_manutencao      |  date  |
| data_fim_manutencao         |  date  |
| Equipamento                | text   |

---

## Descrição do Processo de Transformação

1. **Fonte de Dados**: 
   - O código se conecta ao PowerPlatform Dataflows e navega pelos IDs do workspace e do dataflow para acessar a tabela `bi_si`.

2. **Adição de Colunas Condicionais**:
   - A coluna `Usina` é adicionada usando uma série de condições `if` baseadas no valor de `cod_instalacao`. Dependendo do código, a usina correspondente é atribuída.
   - A coluna `Complexo Eólico` é criada com base na coluna `Usina`, associando cada usina ao seu respectivo complexo eólico.

3. **Filtragem de Linhas**:
   - A tabela `bi_si` é filtrada para incluir apenas registros cuja `data_inicio` seja posterior a 1º de janeiro de 2023.

4. **Mesclagem de Tabelas**:
   - A tabela `bi_si` é mesclada com a tabela `bi_si_equipamento` utilizando a coluna `cod_si` como chave para a junção. A função `Table.NestedJoin` realiza essa junção, trazendo os dados dos equipamentos associados a cada SI - Solicitação de Intervenção.
   - A coluna resultante da junção é expandida usando a função `Table.ExpandTableColumn`, que adiciona os dados de Equipamento à tabela principal `bi_si`.

5. **Seleção de Colunas**:
   - Finalmente, a tabela `bi_si` é reduzida para conter apenas as colunas essenciais, removendo colunas intermediárias e temporárias.

# Documentação da Tabela `bi_si_equipamento`

## Estrutura da Tabela

| Coluna                    | Tipo   |
|---------------------------|--------|
| Usina                     | text   |
| Equipamento                | text   |
| excluido                  |        |
| Complexo Eolico            | text   |
| cod_si                    | number  |
| cod_instalacao            | number  |

---

## Descrição do Processo de Transformação

1. **Fonte de Dados**: 
   - Similar à tabela `bi_si`, a tabela `bi_si_equipamento` é carregada a partir do PowerPlatform Dataflows. Ela contém informações detalhadas sobre os equipamentos relacionados a cada SI.

2. **Adição de Colunas Condicionais**:
   - A coluna `Usina` é adicionada com base em condições `if` na coluna `cod_instalacao`, classificando cada equipamento pela usina correspondente.
   - A coluna `Complexo Eólico` é criada associando a `Usina` ao complexo eólico correto.

3. **Mesclagem de Colunas**:
   - A coluna `Equipamento` é gerada concatenando o código de instalação (`cod_instalacao`) e a descrição de localização (`desc_localizacao`), criando uma identificação mais clara de cada equipamento.

4. **Filtragem e Ordenação**:
   - A tabela `bi_si_equipamento` é ordenada pela coluna `cod_si` em ordem decrescente para garantir que os registros mais recentes sejam priorizados.
   - As linhas são filtradas para incluir apenas aquelas onde a coluna principal é igual a "Sim", limitando a análise aos registros mais importantes (Eventos Principais, desconsiderando equipamentos dos subeventos).

5. **Seleção de Colunas**:
   - Apenas as colunas essenciais são mantidas, como `Usina`, `Equipamento`, `Complexo Eolico`, e `cod_si`.

# Documentação da Tabela `bi_si_hist`

## Estrutura da Tabela

| Coluna           | Tipo    |
|------------------|---------|
| cod_motivo       | number  |
| Hora             | time    |
| Tipo do Motivo   |  text   |
| desc_comentario  |  text   |
| desc_si_acao     |  text   |
| Complexo Eolico  |  text   |
| dthr_historico   | date    |
| cod_si           | number  |
| cod_si_acao      | number  |

---

## Descrição do Processo de Transformação

1. **Fonte de Dados**:
   - A tabela `bi_si_hist` é carregada a partir do PowerPlatform Dataflows, navegando pelos IDs do workspace e dataflow para acessar a entidade correta.

2. **Mesclagem de Tabelas**:
   - A tabela `bi_si_hist` é mesclada com a tabela `bi_motivo`, utilizando o campo `cod_motivo` como chave de junção. A coluna `desc_motivo` da tabela `bi_motivo` é expandida e renomeada como "Tipo do Motivo" para fornecer mais contexto sobre o motivo relacionado ao evento de histórico.
   - Em uma segunda mesclagem, a tabela `bi_si_hist` é combinada com a tabela `bi_si_equipamento`, utilizando o campo `cod_si` como chave para trazer as informações de `Complexo Eolico` para a análise.

3. **Filtragem e Classificação**:
   - Após a mesclagem, a tabela é ordenada pela coluna `dthr_historico` em ordem decrescente, garantindo que os eventos mais recentes apareçam primeiro.
   - A tabela é filtrada para incluir apenas os registros onde a data e hora de histórico (`dthr_historico`) seja posterior a 1º de janeiro de 2023, limitando a análise aos eventos mais recentes.

4. **Criação de Colunas - Hora**:
   - Uma nova coluna chamada "Hora" é criada a partir do campo `dthr_historico`, extraindo o valor de tempo para facilitar análises temporais específicas.

5. **Transformação de Tipos**:
   - A coluna `dthr_historico` é convertida para o tipo `date` para melhorar a precisão na análise de datas.
   - A coluna `cod_si_acao` é convertida para o tipo `number` para garantir a consistência dos dados.

6. **Seleção de Colunas**:
   - Após todas as transformações, apenas as colunas essenciais são mantidas na tabela final, removendo colunas temporárias e irrelevantes, como `excluido` e outras que não são necessárias para a análise.
  
# Documentação da Tabela `bi_motivo`

## Estrutura da Tabela

| Coluna        | Tipo    |
|---------------|---------|
| desc_motivo   |  text   |
| cod_motivo    | number  |

## Descrição do Processo de Transformação

1. **Fonte de Dados**:
   - A tabela `bi_motivo` é carregada do PowerPlatform Dataflows, e contém informações sobre os motivos de ações de SIs.

2. **Seleção de Colunas**:
   - Após o carregamento, a tabela é filtrada para conter apenas duas colunas: `desc_motivo` e `cod_motivo`, fornecendo as descrições dos motivos e seus respectivos códigos.

# Documentação da Tabela `bi_si_registro`

## Estrutura da Tabela

| Coluna     | Tipo   |
|------------|--------|
| cod_motivo | number  |
| cod_si     | number  |

## Descrição do Processo de Transformação

1. **Fonte de Dados**:
   - A tabela `bi_si_registro` é carregada a partir do PowerPlatform Dataflows, navegando pelos IDs de workspace e dataflow para acessar a entidade `bi_si_registro`, que contém registros sobre as SIs.

2. **Seleção de Colunas**:
   - Após o carregamento da tabela completa, a transformação aplica a função `Table.SelectColumns`, que mantém apenas as colunas `cod_motivo` e `cod_si`. Isso remove colunas desnecessárias, garantindo que apenas as informações essenciais sejam mantidas para a análise final, otimiza a performance e facilita o processamento dos dados.

4. **Estrutura Final da Tabela**:
   - A tabela final contém apenas as duas colunas essenciais: `cod_motivo`, que representa o código do motivo associado ao registro, e `cod_si`, que identifica a Solicitação de Intervenção (SI) relacionada.
  
# Documentação da Tabela `Complexo`

## Estrutura da Tabela

| Coluna           | Tipo   |
|------------------|--------|
| Complexo Eolico  |  text   |

## Descrição do Processo de Transformação

1. **Fonte de Dados**: 
   - A tabela `bi_si_equipamento` é carregada a partir do PowerPlatform Dataflows, acessando os dados da entidade `bi_si_equipamento` por meio do workspace especificado.

2. **Adição de Colunas Condicionais**: 
   - A coluna `Usina` é adicionada utilizando uma lógica condicional baseada no código de instalação (`cod_instalacao`). Dependendo do valor da instalação, a usina correspondente é atribuída.
   - A coluna `Complexo Eólico` é adicionada, associando cada usina ao seu respectivo complexo eólico.

3. **Classificação e Filtragem**: 
   - A tabela é classificada pela coluna `cod_si` em ordem decrescente para priorizar os registros mais recentes.
   - As linhas onde o valor de `principal` é "Sim" são mantidas para garantir que apenas os registros principais sejam considerados.

4. **Remoção de Duplicatas**: 
   - Duplicatas baseadas na coluna `Complexo Eolico` são removidas, garantindo que apenas valores únicos sejam mantidos.

5. **Filtragem Final**: 
   - As linhas onde o campo `Complexo Eolico` está vazio ou contém valores irrelevantes são removidas da tabela.

6. **Seleção de Colunas**: 
   - A tabela é reduzida para conter apenas a coluna `Complexo Eolico`, descartando outras colunas temporárias e irrelevantes.

O objetivo dessa coluna é para ser usado como um filtro na tela principal do Relatório.

