# Corretor_Sim

## Descrição
Este é um script  que automatiza a correção de testes e simulados usando folhas de respostas escaneadas.  Usa softaware Formscanner, linguagem R e Rstudio.  O script gera varios arquivos de resultado para diversos fins além de um relatório em PDF para avaliação pela coordenação da escola. 

## Tabela de conteúdos

-[Preparação das provas](#preparação das provas)
-[Instalação](#instalação)
-[Configuração](#configuração)
-[Exemplo de uso](#Exemplo de uso)


## Preparação das provas

   Este script foi elaborado usando o softaware livre Formscanner 1.1.3 mas pode ser adaptado para qualquer outro *OMR Optical Mark Reader*. O softaware pode ser baixado do sote do projeto [https://sites.google.com/site/examgrader/formscanner]( https://sites.google.com/site/examgrader/formscanner).
    Os tutoriais sobre como usar o softaware estão disponíveis em video pelo [próprio desenvolvedor em inglês] (https://youtube.com/playlist?list=PLoXzbRApmhNlCKwpSqtNt5MiwFVvx-u-k&si=lC9vLSM275NY6LLL) 

    As folhas de resposta podem ser customizadas de várias maneiras diferentes. O aquivo  bubleform_angular.xlsx contem diversos exemplos que podem ser alterados. O tutorial em video [forms](https://youtu.be/MnLkv3-QqFs?si=oc4-OSKw4MYJv8IE) mostra como usar. Ambos materias feitos pelos desenvolvedores. 

    Então, basta diagramar a folha de respostas com o número de questões/alternativas que vai usar, imprimir e distribuir no dia da prova. 

### Digitalização das respostas

    As folhas de respostas devem ser escaneadas e convertidas em aquivos de imagem jpg individualmente. Exixtem milhares de formas de fazer isso dependendo do equipamento que você tem à disposição. 


## Instalação

Para usar este script você precisa instalar:

- O softaware Formscanner
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

Para maiores informações veja o [tutorial em video] ( https://youtu.be/I1H8Wj6Tfp4?si=_T3BUpxAhP4Yap5j)


**Dica**
1. Deixe os campos para os registros de matricula/número de chamada por último.

2. Para facilitar a leitura, antes de salvar o template, coloque um preprocessamento de imagem recortando as margens: clique em **Settings**  depois em **Options**, escolha a aba **Image preprocessing** e coloque um valor em pixels que será ignorado em cada margem. Vai aparecer um retangulo azul sobre o template, este não deve estar próximo de nenhuma marca de leitura.



### O script

   Abra o script Corretor_Sim.md no Rstudio. Será necessário alterar algumas informações para customiza-lo

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

ESTA FUNÇÃO precisa ser atualizada toda vez que a quantidade de questões, criterios mudarem entre um simulado e outro.

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
## Carregar o Template

1. Na aba **Template**, clique em **Load Saved Template** e localize o arquivo do template que você salvou.
2. Clique em **Open** para carregar o template.

## Carregar Formulários dos Alunos

1. Clique no ícone azul de arquivos.
2. Selecione os arquivos de imagem dos formulários de teste dos alunos (use Ctrl para selecionar múltiplos arquivos).
3. Clique em **Open**, seguido pelo botão azul de Play para iniciar a coleta de dados.

Após esses passos, você poderá analisar os resultados utilizando a ferramenta de análise do FormScanner e exportar os dados para um arquivo CSV para fácil leitura ou upload ao gradebook do Canvas[1][4][6].

Citations:
[1] https://isit.arts.ubc.ca/tool/formscanner/
[2] https://community.glideapps.com/t/populate-a-form-by-scanning-a-qr-code/69957
[3] https://www.caselle.com/help/cx_help_files/aa0/Forms/How_do_I_scan_a_paper_form_to_use_as_a_form_layout_.htm
[4] https://sites.google.com/site/examgrader/video-tutorials
[5] https://docs.tenable.com/vulnerability-management/Content/Scans/Templates.htm
[6] https://sites.google.com/site/examgrader/quickstart/quickstart-formscanner
[7] https://www.youtube.com/watch?v=I1H8Wj6Tfp4
