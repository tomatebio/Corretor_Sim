# Corretor_Sim

## Descrição
Este é um script que automatiza a correção de testes e simulados usando folhas de respostas escaneadas. Utiliza o software Formscanner, a linguagem R e o RStudio. O script gera vários arquivos de resultado para diversos fins, além de um relatório em PDF para avaliação pela coordenação da escola. 

## Tabela de conteúdos

-[Preparação das provas](#preparação_das_provas)

-[Instalação](#instalação)

-[Configuração](#configuração)

-[Como usar](#como_rodar)

-[Exemplo de uso](#exemplo_de_uso)


## Preparação das provas

  Este script foi elaborado usando o software livre Formscanner 1.1.3 mas pode ser adaptado para qualquer outro *OMR Optical Mark Reader*. O software pode ser baixado do sote do projeto [https://sites.google.com/site/examgrader/formscanner]( https://sites.google.com/site/examgrader/formscanner).
    Os tutoriais sobre como usar o software estão disponíveis em vídeo pelo [próprio desenvolvedor em inglês] (https://youtube.com/playlist?list=PLoXzbRApmhNlCKwpSqtNt5MiwFVvx-u-k&si=lC9vLSM275NY6LLL) 

As folhas de resposta podem ser customizadas de várias maneiras diferentes. O aquivo  bubleform_angular.xlsx contem diversos exemplos que podem ser alterados. O tutorial em vídeo [forms](https://youtu.be/MnLkv3-QqFs?si=oc4-OSKw4MYJv8IE) mostra como usar. Ambos materiais feitos pelos desenvolvedores. 

Então, basta diagramar a folha de respostas com o número de questões/alternativas que vai usar, imprimir e distribuir no dia da prova. 


### Digitalização das respostas

As folhas de respostas devem ser escaneadas e convertidas em aquivos de imagem jpg individualmente. Existem milhares de formas de fazer isso dependendo do equipamento que você tem à disposição. 


## Instalação

Para usar este script você precisa instalar:

- O software Formscanner
- O interpretador da linguagem R
- A IDE Rstudio (opcional)


  A instalação do Formscanner depende de qual sistema operacional você utiliza sendo bastante intuitivo. Tutorial para [Windows] (https://youtu.be/7jau0JZKGpA) e [linux] ( https://youtu.be/kL2utYqHhlo)

  A instalação do interpretador R e o RStudio pode ser feita seguindo o [tutorial oficial em inglês] ( https://posit.co/download/rstudio-desktop/) ou na [demonstração de como instalar] ( https://youtu.be/PgTGLHAGdIE?si=6D9lIJliv-3rPI-7) em português.  

## Configuração

### Formscanner
   É necessário criar um template de leitura (mascara) das folhas no Formscanner. Para isso:


1. **Acesse o FormScanner**:
   - Abra o aplicativo FormScanner em seu computador.

2. **Criar Template**:
   - Vá até a aba **Template** e clique em **Create Template from Image**.
   - Localize um arquivo JPG de qualquer formulário que você deseja usar como base e clique em **Open**.

3. **Adicionar Campos**:
   - Na janela pop-up, clique no grande símbolo de **mais (+)** no canto superior direito.
   - Na aba **Field type**, selecione **Questions by rows** e clique em **confirm**.

4. **Definir Propriedades do Campo**:
   - Preencha a seção **Field properties** de acordo com os parâmetros do seu teste.
   - Clique em **Confirm**.

5. **Ajustar Valores dos Campos**: (necessário para ler os números de chamada dos estudantes)
   - Dê um duplo clique no valor **A** na linha superior e substitua por **0**.
   - Repita este passo para substituir as letras B, C, D, etc., por 1, 2, 3, até H se tornar 9.
   - Clique em **Confirm**.

6. **Salvar o Template**:
   - Clique em **Save template** e escolha um local no seu computador para salvar o arquivo.

Para maiores informações veja o [tutorial em vídeo] ( https://youtu.be/I1H8Wj6Tfp4?si=_T3BUpxAhP4Yap5j)


**Dica**
1. Deixe os campos para os registros de matricula/número de chamada por último.

2. Para facilitar a leitura, antes de salvar o template, coloque um preprocessamento de imagem recortando as margens: clique em **Settings**  depois em **Options**, escolha a aba **Image preprocessing** e coloque um valor em pixeis que será ignorado em cada margem. Vai aparecer um retângulo azul sobre o template, este não deve estar próximo de nenhuma marca de leitura.


#### Como ler as respostas no Formscanner
##### Carregar o Template

1. Na aba **Template**, clique em **Load Saved Template** e localize o arquivo do template que você salvou.
2. Clique em **Open** para carregar o template.

##### Carregar Formulários dos Alunos

1. Clique no ícone azul de arquivos.
2. Selecione os arquivos de imagem dos formulários de teste dos alunos (use Ctrl para selecionar múltiplos arquivos).
3. Clique em **Open**, seguido pelo botão azul de Play para iniciar a coleta de dados.

Após esses passos, você poderá analisar exportar os dados para um arquivo CSV e usar no script.


### O script

   Abra o script Corretor_Sim.md no Rstudio. Será necessário alterar algumas informações para customizá-lo

   No inicio do texto estão os itens de autoria do relatório como titulo, subtitulo, autor(es), data (linhas 2-5). Reescreva para adequar ao teu uso, não esqueça que deve estar em aspas " ". Não altere os outros campos 

``` R 
2 title: "Relatório do Simulado"
3 subtitle: "1 simulado"
4 author: "Coordenação"
5 date: "05 novembro de 2024"

```
Depois é necessário mudar os seguinte campos (linhas 30-35). O campo data coloque a DATA de realização do simulado, no campo FOLHAS_ESCANEADAS o nome do arquivo que contem as respostas dadas pelos alunos lidas pelo formscanner; INFO_ALUNOS o arquivo que contem os dados dos alunos; GABARITO, o gabarito; DISCIPLINAS, a composição das disciplinas. 

A formatação desses arquivos está explicada [mais abaixo] (#arquivos) 


``` R 
30 DATA <-"10 de janeiro"                                                                                    
31 # Substitua os nomes dos arquivos                                                                         
32 FOLHAS_ESCANEADAS<-c("escaneados_cabecalhos.csv")                                                         
33 INFO_ALUNOS<-c("Registro_alunos.csv")                                                                     
34 GABARITO<-c("Gabarito.csv")                                                                               
35 DISCIPLINAS<- c("disciplinas.csv")                                                                        

```  
Nas linhas 37-41 é necessário indicar em quais colunas do arquivo referenciado em FOLHAS_ESCANEADAS começam as respostas das questões, COL_QUEST_INICIO; em qual termina, COL_QUEST_FINAL; assim como em qual coluna começa e termina os números do Registro de matricula COL_RM_INICIO e COL_RM_FINAL, respectivamente.

``` R                                                                                                         
37 #identifique as colunas do seu arquivo                                                                    
38 COL_QUEST_INICIO<-2  # Primeira coluna com questões                                                        
39 COL_QUEST_FINAL<- 16 # Ultima coluna com questões                                                         
40 COL_RM_INICIO<- 17# Primeira coluna com o RM                                                              
41 COL_RM_FINAL <- 24 # ultima coluna com o RM   

```  

Por fim, é necessário configurar a função de atribuição de nota. A função ativa está configurada nos padrões do Centro Paula Souza que utiliza menções I,R,B,MB e pode ser facilmente convertida para sistema A,B,C,D. 

No exemplo abaixo quem acerta 5 ou menos recebe "I", quem acertar mais que 5  e até 7 recebe "R". Quem acertar entre 8 e 12 recebe "B" e quem fizer mais que 12 recebe "MB"

    Esta função precisa ser atualizada toda vez que a quantidade 
    de questões, criterios mudarem entre um simulado e outro.

```R
44 # Função para atribuir nota/menção, ela recebe o vetor/coluna que tem a soma dos                          
45 # acertos e o numero de questões total para retornar a mencao.                                            
46 #  O exemplo abaixo atribui:
47 # Menção "I" para quem fizer 5 ou menos, "R" para acertar entre 6ou 7
48 # "B" para quem fizer entre 8 até 12 e "MB" para quem fizer 13 ou mais. 
49 # 
50 # Ela pode ser facilmente ajustada para outros números de acertos e 
51 # também para sistema A,B,C,D. Pode ser usada para retornar menção por disciplina. 
52 # 
53 # 
54 Atribuir_nota <- function(totais,questoes) {
55   ifelse(totais <= 5, "I",
56          ifelse(totais <= 7, "R",
57          ifelse(totais <= 12, "B", "MB")))
58 }


```

Mais abaixo (linhas 64-66) existe uma função alternativa usando porcentagem de acertos como nota final. Para usá-la é necessário comentar as linhas 54-58 e descomentar as linhas 64-66.

### Arquivos

    Todos os arquivos são .csv usando o ";" como separador de campos
    e aspas para separar texto.

    O R é "case sensitive" então há diferença entre "TURMA", "Turma"
    e "turma". Preste atenção para não ter problemas com isso, mantenha a consistência.

São necessários 4 arquivos diferentes para o script funcionar. Todos devem estar no mesma pasta que o **Corretor_Sim.Rmd**. Vou referenciar pelo nome dado no script.


**FOLHAS_ESCANEADAS**: É a saída do FormScanner modificada com a respostas dos alunos. É necessário alterar o cabeçalho das colunas que deve ficar: "arquivo"; "Q1"; "Q2"; "Q3"; ...."RM";"RM1";"RM2";"RM3";"RM4";"RM5". Onde, Q1, Q2 são os números das questões e RM para cada digito do registro da matricula. No exemplo, é o arquivos "escaneados_cabeçalho.csv"

**INFO_ALUNOS**: Contem as informações dos alunos em uma única planilha. Deve conter, obrigatoriamente, as colunas: "RM", com os registro de matricula; "Turma", com identificador curto da turma  como "1 A Mec"; "Curso", identificador do curso como "Edificações", "Mecânica", "Alimentos noturno"; "Nome", nome completo do estudante. Pode conter outros campos como "email", "telefone" etc. 

Nenhum dado das colunas obrigatórias pode estar em branco.   Se não houver cursos diferentes você pode usar para períodos "manhã", "tarde" ou colocar "Ensino Médio" para todos. Mas não pode alterar o nome da coluna. No exemplo é "Registro_alunos.csv"

**GABARITO**: Possui as repostas corretas para as questões. Possui uma coluna para cada questão sendo a primeira linha com o identificador da questão: "Q1" , "Q2"... e na segunda linha a opção correta: "A", "B", etc. Caso precise cancelar uma questão coloque "X" como resposta e a questão não será somada.  

**DISCIPLINAS**: Contem a divisão da prova em disciplinas. A primeira coluna tem o cabeçalho "disciplina" e traz a sigla da disciplina na ordem que aparece na prova. A segunda coluna tem o cabeçalho "numero" e traz a quantidade de questões de cada disciplina na prova. No exemplo é o arquivo "disciplinas.csv"


## Resultados

O Script deve, em condições normais, tabular os dados de repostas dos alunos, referenciar as respostas com os dados dos alunos. Gerar estatísticas  e gráficos. Além disso, deve gerar os seguintes arquivos dentro da pasta de trabalho:
   - O relatório em PDF com todos os dados do simulado. Por padrão o nome do relatório é "Corretor_sim.pdf".
   - O arquivo "Resumo de acertos.csv" contem o RM, número de acertos em cada disciplina e a Menção final em uma única planilha. Sendo útil para integração com outros sistemas.
   - O arquivo "Calculo de acertos.csv" é a versão expandida do "Resumo". Contem o nome do arquivo escaneado, o acerto ou erro em cada questão além dos dados que estão no "Resumo". Útil para verificar inconsistências.

   - O arquivo "Resultado_ professores.xls" contem o RM, turma, Nome, número de acertos por disciplina e menção dos alunos organizados por turma em abas.
   - O arquivo "Para_Mala_direta.csv" está organizado para a divulgação dos resultados diretamente aos alunos ou responsáveis. Além dos dados que estão em outros arquivos este arquivo contem todos os outros dados que estavam em INFO_ALUNOS como, por exemplo, o email de cada um. 

    Os arquivos foram assim organizados para evitar a divulgação de dados fora do contexto essencialmente necessário. 


## Como usar

1 Colocar todos os aquivos necessários na mesma pasta que o script Corretor_sim;
2 Configurar os arquivos como mencionado acima;
3 Abra o script com dois cliques no Rstudio;
4 Encontre o icone azul do novelo, clique na seta para baixo e escolha "knit to PDF". 
 
## Exemplo de uso

Na pasta *Exemplo* contem os arquivos de um simulado pequeno com dados fictícios para usar. As folhas de resposta escaneadas fazem parte do conjunto de teste do próprio FormScanner e os dados dos alunos, gabaritos e disciplinas foram inventados. 



## To do list
  - Organizar pasta de entrada e saída


