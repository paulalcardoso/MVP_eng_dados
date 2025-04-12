# MVP_eng_dados
# **MVP - Sprint Engenharia de Dados - Paula Alcantara Cardoso**
## **_O host_effect dos Jogos Olímpicos de Verão_**
Os Jogos Olímpicos são o maior evento esportivo mundial, divididos entre as edições de verão e de inverno, que acontecem a cada dois anos. Com o objetivo de promover paz, união e respeito, atletas de todo o mundo competem em mais de 400 eventos.

Originados na Grécia Antiga, os Jogos tinham a finalidade de cultuar os deuses do Olimpo por meio das habilidades dos atletas. Com o crescimento do cristianismo e a ascensão do imperador romano Teodósio, os Jogos foram banidos em 394 d.C., por serem considerados uma festa pagã. Somente em 1896 as competições foram retomadas, dando início aos Jogos Olímpicos da Era Moderna. Os Jogos de Inverno, por sua vez, foram criados em 1924, com foco nos esportes praticados em ambientes frios ou com neve, como o esqui e a patinação no gelo.

Atualmente, além das edições de verão e de inverno, existem também os Jogos Paralímpicos e os Jogos Mundiais da Juventude. No Brasil, há a tradição de acompanhar e torcer pelos atletas nos Jogos de Verão, e nos últimos anos, os Jogos Paralímpicos também têm ganhado maior repercussão. 

REFERÊNCIAS: 

1. https://www.olympics.com/pt/olympic-games
2. https://revistagalileu.globo.com/Sociedade/Historia/noticia/2021/07/olimpiadas-conheca-historia-os-simbolos-e-importancia-dos-jogos.html
3. https://pt.wikipedia.org/wiki/Jogos_Ol%C3%ADmpicos_de_Inverno 

Quando se fala sobre o impacto de sediar os Jogos de Verão, há uma grande expectativa de que o país anfitrião quebre seu recorde de medalhas por edição. Isso ocorre devido ao maior investimento nos esportes, ao aumento no número de atletas representando a nação e, não menos importante, ao maior incentivo do público por competir em casa. Esse fenômeno é conhecido como "host-effect" ou "efeito-anfitrião". 

O objetivo deste MVP é analisar exclusivamente as medalhas conquistadas pelos países que já foram sede dos Jogos Olímpicos de Verão e responder as seguintes perguntas:

### **1. Historicamente, os países sede dos Jogos Olímpicos de Verão batem seu recorde de medalha em sua edição?** 

### **2. Quais são os TOP 3 esportes em que os países que já foram anfitriões mais ganharam medalhas?**

### **Detalhamento do desenvolvimento do MVP:**

#### **Busca pelos dados e Coleta:**

Os dados utilizados neste MVP foram baixados do seguinte link: https://www.kaggle.com/datasets/piterfm/olympic-games-medals-19862018. Optei por esse conjunto de dados por ele reunir informações de diversas edições dos jogos, o que é necessário para responder as minhas perguntas com maior exatidão.

Nessa base de dados existem 4 tabelas: 
- olympic_athletes, 
- olympic_hosts, 
- olympic_medals e 
- olympic_results

Como as perguntas propostas neste MVP são orientadas aos países que já sediaram alguma edição dos Jogos Olímpicos de Verão, a tabela olympic_athletes não será importada, uma vez que ela traz informações pessoais dos atletas, como o ano de nascimento, primeira participação nos jogos, número de medalhas conquistadas e link para perfil profissional. Além disso, a tabela olympic_results possui muitas das colunas encontradas nas tabelas olympic_hosts e olympic_medals, sem adicionar mais informações que ajudariam a responder as duas perguntas aqui propostas. 

As tabelas olympic_hosts e olympic_medals foram baixadas e carregadas no DBFS. Em seguida, essas tabelas foram carregadas no notebook usando Spark.

#### **Modelagem:**

Optei por construir um modelo do tipo flat (Um diagrama do modelo criado está disponível no repositório do Github) pelo seu poder analítico que facilita a criação de consultas SQLs, somatórios, agrupamentos e filtros, que foram amplamente explorados neste MVP. Essa modelagem foi especialmente útil para a análise de desempenho por país, por edição e por modalidade dos Jogos Olímpicos, permitindo responder com maior clareza as perguntas propostas, uma vez que todas as informações úteis para as resoluções estavam reunidas em uma única tabela. 

Os dados das medalhas e dos países-sede são organizados de acordo com o catálogo a seguir:

1. OLYMPIC_HOSTS:
  - game_slug: conjunto de caracteres do identificador único e padronizado da edição dos Jogos Olímpicos
  - game_end_date: data/hora do fim das edições dos Jogos Olímpicos
  - game_start_date: data/hora do início das edições dos Jogos Olímpicos
  - game_location: conjunto de caracteres que nomeiam o país sede das edições dos Jogos Olímpicos
  - game_name: conjunto de caracteres que dão o nome das edições dos Jogos Olímpicos
  - game_season: conjunto de caracteres que identificam o tipo dos Jogos Olímpicos
  - game_year: número do ano da edição dos Jogos Olímpicos

2. OLYMPIC_MEDALS:
  - disciplie_title: conjunto de caracteres que indicam o nome da categoria geral das modalidades esportivas
  - slug_game: conjunto de caracteres do identificador único e padronizado da edição dos Jogos Olímpicos
  - event_title: conjunto de caracteres que identificam o nome específico do esporte da modalidade
  - event_gender: conjunto de caracteres que definem o gênero do evento
  - medal_type: conjunto de caracteres que identificam o tipo da medalha conquistada
  - participant_type: conjunto de caracteres que definem o tipo de participação no evento esportivo
  - participant_title: conjunto de caracteres que definem o nome da equipe que recebeu medalha
  - athlete_url: conjunto de caracteres que identifica o link para o perfil profissional do atleta
  - athlete_full_name: conjunto de caracteres que identifica o nome completo do atleta
  - country_name: conjunto de caracteres que identifica o nome completo do país representado
  - country_code: conjunto de caracteres que identifica o país
  - country_3_letter_code: conjunto de caracteres padronizado pelo COI que identifica os países

# **Adicionar imagem das tabelas**

#### **Carga:**

No databricks, é comum a utilização categorias ao criar tabelas. de Na etapa da criação das Tabelas Bronze, salvei os dados brutos, isto é, conforme eles vieram no banco de dados. Já na etapa Silver, realizei correções nos dados das tabelas e eliminei colunas que não agregam informações cruciais para responder as perguntas deste MVP. Finalmente, na tabela Ouro, uni as tabelas usando a coluna que ambas possuem em comum. Para mais informações sobre as tabelas Silver, Bronze e Ouro, acompanhe as minhas explicações no decorrer deste notebook.

Dentre os tratamentos realizados nas tabelas Silver, estão:

- Tabela silver.summer_olympics
1. Precisei corrigir o nome de alguns países que já sediaram alguma edição dos Jogos Olímpicos de Verão. Inicialmente encontrei apenas um erro, que pude corrigir ainda na etapa de criação da tabela, porém outros nomes precisaram ser alterados usando UPDATE uma vez que a tabela já havia sido criada.
2. Filtrei as linhas para que a tabela tivesse apenas as edições dos jogos de verão, i.e., selecionei filtrei a coluna game_season pelo valor 'Summer', uma vez que minha análise é apenas para essa classe dos Jogos Olímpicos
3. Selecionei apenas as colunas game_slug, game_location e game_year da tabela bronze, uma vez que game_slug é o identificador único das edições, game_location possui o nome dos países anfitriões das edições e game_year indica o ano da edição em questão. 

- Tabela silver.olympic_medals_table:
1. Identifiquei a necessidade de tranformar a coluna medal_type em 3 colunas diferentes, uma para cada tipo de medalha, já que precisaria somar a quantidade de medalhas de cada um dos países.
2. Precisei tratar os dados para que eles fossem orientados aos países, e não aos atletas. Isso foi necessário pois eventos disputados em duplas (independente do gênero) contabilizavam as medalhas por atleta, abrindo margem para a interpretação de que o país havia recebido duas medalhas, e não uma. Além disso, com a criação das colunas específicas para cada medalha, identifiquei a necessidade de tratar os casos de empate entre atletas da mesma nacionalidade. Ambos esses tratamentos foram feitos separadamente e, por não haver possibilidade de duplicidade de linhas, os resultados foram combinados usando o UNION ALL. Mais informações e explicações sobre as lógicas aqui aplicadas, procure pela sessão "Criando a tabela Silver para os dados olympic_medals"!

A tabela Ouro, por sua vez, foi criada unindo as tabelas silver.summer_olympics e silver.olympic_medals pelas colunas game_slug e slug_game, respectivamente, preservando as medalhas de todos os países presentes no banco de dados, e não apenas os dos que já foram anfitriões. Optei por fazer assim para, caso quisesse aumentar as minhas perguntas, eu não ficasse limitada.

#### **Análise:**
**Qualidade dos dados**

O banco de dados utilizado possui registros detalhados de múltiplas edições dos Jogos Olímpicos de Verão e de Inverno, contemplando diversos esportes, modalidades e países. Os dados são bem separados e os atributos são bem definidos, como é o caso das colunas da tabela de medalhas.

Conforme as tabelas da categoria Silver estavam em processo de criação, precisei verificar fatos históricos para validar a veracidade e a consistência dos dados, como os anos das edições, nomes de atletas, de equipes e de países. O único ponto com inconsistência detectada foram os nomes de alguns países, o que poderia comprometer a fidelidade das análises dos países-sede.

Por fim, os dados originais da tabela de medalhas (salvos na tabela bronze.olympic_medals) são orientados para os atletas medalhistas. Na minha percepção, isso ocorre para que seja possível relacionar esta tabela com a tabela dos atletas presente no banco de dados, porém não utilizada neste MVP. Como consequência, os dados foram tratados para conseguir recriar um quadro de medalhas para cada um dos países, evitando que eventos disputados por duplas ou equipes gerassem duplicações na contagem de medalhas por país. 

**Solução do problema**

O principal objetivo deste MVP foi investigar se os países que sediaram os Jogos Olímpicos de Verão obtiveram seu melhor desempenho histórico em número de medalhas durante a edição em que foram anfitriões, e identificar as modalidades esportivas em que esses países mais se destacaram ao longo do tempo.

Para isso, duas análises principais foram desenvolvidas a partir do modelo flat e consultas SQL. Vale ressaltar que todas as análises consideram os dados disponíveis até Tóquio 2020, e não incluem ainda a edição de Paris 2024.

Para responder a primeira pergunta, foi feita uma comparação, para cada país que já foi sede, o total de medalhas conquistadas na edição em que sediou os Jogos com o maior número de medalhas conquistado em edições anteriores. Para isso, foram utilizadas expressões com MAX() e a função COALESCE, organizadas com CTEs que permitiram isolar os dados por país, ano e total de medalhas. Como resultado, foi possível identificar que em 62,07% das edições dos Jogos Olímpicos de Verão os países anfitriões bateram seus recordes de medalhas em suas edições.

A segunda análise teve como objetivo entender quais esportes mais contribuíram para as medalhas dos países que já foram sede. Foi realizada uma agregação do total de medalhas por discipline_title e country_name, seguida da aplicação da função DENSE_RANK() para identificar as três modalidades com mais medalhas de cada país (incluindo empates). Essa pergunta foi respondida com a geração de uma lista contendo as modalidades mais representativas para cada país-sede analisado. A partir desse resultado, a análise foi estendida para identificar quais modalidades mais apareceram entre os top 3 dos países e quais foram as menos recorrentes. Com isso, foi possível destacar que foram identificados 16 modalidades diferentes para os 20 países-sede e o Atletismo foi, disparado, a modalidade mais presente entre os países analisados, enquanto o Ciclismo de Estrada apareceu apenas uma vez.

#### **Autoavaliação:**

Concluindo este MVP, posso dizer que consegui atingir os objetivos que defini no início do projeto. As duas perguntas principais — sobre se os países-sede melhoram seu desempenho olímpico em suas edições e quais são as modalidades em que mais ganham medalhas — foram respondidas com base nos dados disponíveis.

A parte mais desafiadora foi pensar em uma forma de representar as medalhas por país, já que os dados estavam originalmente focados nos atletas. Isso exigiu bastante pesquisa (inclusive histórica!) pra entender como tratar os empates entre atletas da mesma nacionalidade e esportes me medalham (ou medalhavam) 4 atletas, e não duplicar ou excluir alguma medalha. 

Durante essas semanas de trabalho, li bastante sobre a história dos jogos, me lembrei de momentos marcantes das últimas seis edições (as que acompanhei!) e até conversei muito com a minha família — que é tão apaixonada por esporte quanto eu - e amigos, discutimos o impacto das Olimpíadas na geopolítica (e vice-versa), o que tornou o projeto ainda mais interessante.

No total, foram cerca de três semanas dedicadas ao projeto — desde a escolha do banco, passando por carregar e tratar os dados no Databricks, até conseguir realmente responder às perguntas com segurança. Por causa do tempo investido nisso tudo, resolvi não expandir o número de perguntas, embora tenha ficado com muita vontade de continuar explorando os dados (perceptível pelas curiosidades da última pergunta..).

Pensando em trabalhos futuros:

- Estender a análise de modalidades para todos os países que já ganharam medalhas, buscando identificar a modalidade mais forte de cada um;

- Usar os dados dos atletas (que são riquíssimos!) pra responder perguntas como: quem mais ganhou medalhas? quem participou de mais edições? quais atletas se destacam por longevidade ou versatilidade?

Termino esse MVP com a sensação de que aprendi muito sobre SQL, modelagem de dados e também sobre os próprios Jogos Olímpicos. E também com a vontade de continuar trabalhando com esse banco de dados nos próximos MVPs que virão, afinal, esta é a minha primeira sprint!
