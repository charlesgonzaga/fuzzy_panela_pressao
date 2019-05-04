# Fuzzy - Panela-de-Pressao

## Implementação de um Sistema Especialista Fuzzy para o controle da velocidade de cozimento de uma Panela-de-Pressão
### Acadêmicos: Charle Gonzaga, Cássio Rasveiler.

#### 1. Introdução
<p align="justify">
Neste relatório será detalhado a implementação de um Sistema Especialista Fuzzy utilizando a
ferramenta FuzzyClips. A finalidade do sistema é controlar a velocidade de cozimento de uma panela-de-pressão.
</p>
<p align="justify">
As variáveis lingüísticas de entrada e saída são mostradas na Tabela 1:
</p>

<table>
  <thead>
    <tr>
      <th></th>
      <th colspan='3'>Temperatura 'fogo'</th>
    </tr>
    <tr>
      <th>Pressão</th>
      <th>Baixa</th>
      <th>Média</th>
      <th>Alta</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Baixa</th>
      <td>Muito Lento</td>
      <td>Lento</td>
      <td>Moderado</td>
    </tr>
    <tr>
      <th>Média</th>
      <td>Lento</td>
      <td>Moderado</td>
      <td>Rápido</td>
    </tr>
    <tr>
      <th>Alta</th>
      <td>Moderado</td>
      <td>Rápido</td>
      <td>Muito Rápido</td>
    </tr>
  </tbody>
</table>

###### *Tabela 1. Variáveis lingüísticas para o controle da velocidade de cozimento de uma panela-de-pressão*

#### 2. Implementação e Testes
<p align="justify">
Para cada variável lingüística foi definido um template. No caso do template <b>temperatura</b>, utilizou-se
duas funções pré-definidas (z e s) e uma trapezóide:
</p>
<pre>
(deftemplate temperatura
  0 240 graus
  (
    (baixa (z 0 80))
    (media (60 0)(120 1)(150 1)(240 0))
    (alta (s 200 240))
  )
)
</pre>

###### *A figura 1 ilustra os valores numéricos possíveis para a temperatura, considerando as variáveis lingüísticas definidas na Tabela 1 e no template respectivo.*

#### ![Figura 1!](https://raw.githubusercontent.com/charlesgonzaga/fuzzy_panela_pressao/master/img-template-temperatura.jpg "deftemplate temperatura")
###### *Figura 1. Plotagem dos Valores numéricos possíveis para a temperatura.*

<p align="justify">
No caso do template <b>pressao</b>, utilizou-se
três funções piramidaes:
</p>
<pre>
(deftemplate pressao
  0 20 pressao 
  (
    (baixa (0 0)(5 1)(7 0))
    (media (3 0)(8 1)(13 0))
    (alta (s 8 20))
  )
)
</pre>

###### *A figura 2 ilustra os valores numéricos possíveis para a pressão, considerando as variáveis lingüísticas definidas na Tabela 1 e no template respectivo.*

#### ![Figura 2!](https://raw.githubusercontent.com/charlesgonzaga/fuzzy_panela_pressao/master/img-template-pressao.jpg "deftemplate pressao")
###### *Figura 2. Plotagem dos Valores numéricos possíveis para a pressão.*

<p align="justify">
No caso do template <b>tempo</b>, utilizou-se
cinco funções piramidaes:
</p>
<pre>
(deftemplate tempo
  0 90 tempo 
  (
    (muito_rapido (z 0 30))
    (rapido (15 0)(30 1)(45 0))
    (moderado (30 0)(45 1)(60 0))
    (lento (45 0)(60 1)(75 0))
    (muito_lento (s 60 90))
  )
)
</pre>

###### *A figura 3 ilustra os valores numéricos possíveis para o tempo de cozimento, considerando as variáveis lingüísticas definidas na Tabela 1 e no template respectivo.*

#### ![Figura 3!](https://raw.githubusercontent.com/charlesgonzaga/fuzzy_panela_pressao/master/img-template-tempo.jpg "deftemplate tempo")
###### *Figura 3. Plotagem dos Valores numéricos possíveis para o tempo de cozimento.*

<p align="justify">
As variáveis lingüística foram colocadas em 5 regras distintas. A utilização da declaração da salience foi a solução adotada para garantir que essas regras fossem executadas ANTES da regra de defuzzificação;
</p>

<pre>
(defrule cozimento-muito-rapido
  (declare (salience 10))
  (temperatura alta)
  (pressao alta)
=>
  (assert (tempo muito_rapido))
)
</pre>

<pre>
(defrule cozimento-rapido
  (declare (salience 10))
  (or 
    (and
      (temperatura alta)
      (pressao media)
    )
    (and
      (temperatura media)
      (pressao alta)
    )
  )
=>
  (assert (tempo rapido))
)
</pre>

<pre>
(defrule cozimento-moderado
  (declare (salience 10))
  (or 
    (and
      (temperatura alta)
      (pressao baixa)
    )
    (and
      (temperatura media)
      (pressao media)
    )
    (and
      (temperatura baixa)
      (pressao alta)
    )
  )
=>
  (assert (tempo moderado))
)
</pre>

<pre>
(defrule cozimento-lento
  (declare (salience 10))
  (or 
    (and
      (temperatura media)
      (pressao baixa)
    )
    (and
      (temperatura baixa)
      (pressao media)
    )
  )
=>
  (assert (tempo lento))
)
</pre>

<pre>
(defrule cozimento-muito-lento
  (declare (salience 10))
  (temperatura baixa)
  (pressao baixa)
=>
  (assert (tempo muito_lento))
)
</pre>

<p align="justify">
Para a defuzzificação, foi criada uma variável global e uma regra que também faz a plotagem do
valor numérico encontrado. A regra defuzifica foi declarada com salience 0 de forma a ser executada
posteriormente às demais regras do sistema.
</p>
<pre>
(defglobal
  ?*g_resultado* = 0
)
</pre>
<pre>
(defrule defuzifica
  (declare (salience 0))
  ?v_tmp <- (tempo ?)
=>
  (bind ?*g_resultado* (moment-defuzzify ?v_tmp))
  (plot-fuzzy-value t "*" nil nil ?v_tmp)
  (retract ?v_tmp)
  (printout t "Tempo de cozimento: ")
  (printout t ?*g_resultado* crlf)
  (printout t " >>> Término <<< " crlf)
)
</pre>

<p align="justify">
Foram gerados valores através de cada um dos deffacts, no sentido de testar as regras, e obter os valores
numéricos relacionados aos resultados. O código-fonte de cada um dos testes ilustram a utilização de valores para o tempo de cozimento de uma panela de pressão.
</p>

<pre>
(deffacts saida1
  (temperatura alta)
  (pressao media)
)
</pre>
###### *A figura 4 ilustra a saida do deffacts __saida1__.*

#### ![Figura 4!](https://raw.githubusercontent.com/charlesgonzaga/fuzzy_panela_pressao/master/img-saida1.jpg "plotagem saida1")
###### *Figura 4. Plotagem do deffacts saida1.*

<pre>
(deffacts saida2
  (temperatura baixa)
  (pressao media)
)
</pre>
###### *A figura 5 ilustra a saida do deffacts __saida2__.*

#### ![Figura 5!](https://raw.githubusercontent.com/charlesgonzaga/fuzzy_panela_pressao/master/img-saida2.jpg "plotagem saida2")
###### *Figura 5. Plotagem do deffacts saida2.*

#### 3. Conclusão
<p align="justify">
Um sistema especialista é aquele que tem por finalidade entender o
conhecimento humano de uma determinada área e aplicar regras para substituir o
trabalho humano.
Deste modo realizou-se um projeto cujo objetivo foi criar um programa
especialista que pudesse mensurar o tempo de cozimento de alimentos em uma
panela de pressão, sendo as variáveis de entrada no presente estudo, a temperatura
(fogo) e a pressão .
Neste contexto, os objetivos deste projeto foram alcançados, pois através do
programa, quando o usuário inserir as variáveis de entrada “temperatura” e “pressão”,
o sistema informa o tempo necessário para o cozimento dos alimentos.
Por fim, caso este sistema tenha sua eficácia comprovada, o mesmo facilitará o
controle do cozimento de diversos alimentos.
</p>
