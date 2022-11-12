# Projeto final do curso BI-MASTER 2020-2.
Repositório: bimaster-proj  
Autor: Alex M. Campos

## Descrição
O presente projeto tem como finalidade servir como projeto de final do curso BI-MASTER, da turma 2020-2, para o aluno Alex M. Campos.
A proposta do projeto é realizar uma atividade de análise de anomalias em uma série história com poucas amostras, para explorar a dificuldade da análise em uma série deste tipo e as ferramentas que podem ser utilizadas para a detecção de anomalias. A hipótese é que a detecção de anomalias seria feita em um processo batch, dado que série possui uma baixa frequência, mas também exploraremos abordagens para permitir estabelecer um processo de deteção de anomalias 'stream-like'.

## Sobre a série temporal escolhida
Por ser utilizada como parte do processo de avaliação de desempenho de alguns produtos do BNDES, selecionamos a série IBC-BR para ser o foco da análise. Essa série mede o desempenho da economia do país, mês-a-mês. Tal índice é produzido pelo Banco Central do Brasil e está disponível no portal de dados abertos do BACEN ([IBC-BR](https://dadosabertos.bcb.gov.br/dataset/24363-indice-de-atividade-economica-do-banco-central---ibc-br)). Os dados são gerados a partir de 2003 e, em novembro de 2022, a série possuía 236 valores.

## Análise dos dados

Arquivo: [ibc-br_analise_serie.ipynb](ibc_br_analise_serie.ipynb)  
Processo de análise:
- Obtivemos os dados do Banco Central do Brasil, através de uma API REST, localizada em http://api.bcb.gov.br/dados/serie/bcdata.sgs.24363/dados?formato=csv.
- Verificamos propriedades básicas dos dados (quantidades, estatísticas, presença de valores nulos, etc.).
- Visualizamos os dados como um gráfico de linha.
![Gráfico de linha da série IBC-BR](imagens/ibc-br_grafico_serie.png)
- Decompusemos os dados nos aspectos de 'tendência', 'sazonalidade' e 'resíduo'.
![Gráfico de linha das componentes resultantes da decomposição da série IBC-BR](imagens/ibc-br_grafico_serie_decomposta.png)
- Verificamos que a sazonalidade é bem definida (12 meses).
- Verificamos que a tendência não é linear.
- Verificamos que as anomalias parecem estar contidas no resíduo.
- Analisamos a distribuição dos dados e identificamos que a maioria deles está contida na faixa entre 120 e 140.
- Analisamos as propriedades estatísticas que definem se a série é ou não estacionária (propriedade que desejamos, para poder aplicar alguns métodos na fase de análise de anomalias) e vimos que apenas a sazonalidade e o resíduo da série IBC-BR são estacionárias.
- Como o processo de decomposição da série poderia ser um problema em uma abordagem de detecção de anomalias "stream-like" e a série IBC-BR não era estacionária, decidimos construir uma série derivada, calculando as diferenças entre valores subsequentes na série original e analisar seus resultados.
- Construímos a série de diferenças e verificamos propriedades básicas dos dados (quantidades, estatísticas, presença de valores nulos, etc.)
- Comparamos visualmente as série IBC-BR original e a série de diferenças e avaliamos que a série de diferenças captura as mudanças de comportamento da série original e que parecia estável, sendo um provável proxy da série original para um processo de detecção de anomalias.
![Gráfico de linha comparativo entre a série IBC-BR e a série da diferença](imagens/ibc-br_serie_original_vs_serie_diferencas.png)
- Decompusemos a série de diferenças para analisar o comportamento das componentes de tendência, sazonalidade e resíduo de forma mais detalhada.
![Gráfico de linha da decomposição da série de diferenças](imagens/ibc-br_serie_diferencas_grafico_serie_decomposta.png)
- Notamos que as duas séries, IBC-BR e de diferenças, possuíam valores residuais inválidos em algumas posições, por isso verificamos se a quantidade e as posições desses elementos nulos eram compatíveis nas duas séries e concluímos que eram.
- Comparamos visualmente as duas séries residuais, da série IBC-BR e da série de diferenças, após remover os valores inválidos e normalizar os valores de ambas.
![Gráfico comparativo dos valores residuais das duas séries](imagens/ibc-br_grafico_residuos_normalizados_serie_original_vs_serie_diferencas.png)
- As análises dos dados reforçaram a hipótese de que série de diferenças seria um bom proxy da série IBC-BR. 
- Também verificamos que tanto a série de diferenças quanto o resíduo da série de diferenças são séries estacionárias.
- Calculamos a média móvel com uma janela de 12 meses e desenhamos o gráfico da série e de sua média móvel para as séries IBC-BR e de diferenças, mas a média móvel, fortemente ligada à tendência, não parecia uma boa abordagem para o processo de detecção de anomalias.
![Gráfico da série IBC-BR e de sua média móvel com janela de 12 meses](imagens/ibc-br_grafico_media_movel.png)
![Gráfico da série de diferenças e de sua média móvel com janela de 12 meses](imagens/ibc-br_serie_diferencas_grafico_media_movel.png)
- Analisamos a autocorrelação dos valores da série IBC-BR, com lag de até 24 meses, e concluímos que:
- Os valores da série IBC-BR são autocorrelacionados.
- Os valores da série IBC-BR são autocorrelacionados _negativamente_ a cada 6 meses.
- Os valores da série IBC-BR são autocorrelacionados positivamente, de maneira forte, a cada 7 meses.
- Os valores da série IBC-BR são autocorrelacionados positivamente a cada 12 meses (nossa hipótese inicial).
- Os valores da série IBC-BR são autocorrelacionados _negativamente_, de maneira forte, a cada 13 meses.
![Gráfico de autocorrelação da série IBC-BR](imagens/ibc-br_autocorrelacao.png)
![Gráfico de autocorrelação parcial da série IBC-BR](imagens/ibc-br_autocorrelacao_parcial.png)
- Os valores de sazonalidade são correlacionados positivamente a cada 12 meses (nossa hipótese).
![Gráfico de autocorrelação da sazonalidade da série IBC-BR](imagens/ibc-br_sazonalidade_autocorrelacao_parcial.png)
- Os valores de tendência e resíduo da série IBC-BR não são autocorrelacionados (como esperávamos.)

> FIXME: CONTINUAR DAQUI.