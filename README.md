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

#### imagem aqui

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

#### imagem aqui

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

#### imagem aqui

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
  (assert (Tempo muito_rapido))
)
</pre>

<pre>
(defrule cozimento-rapido
  (declare (salience 10))
  (or 
    and(
      (temperatura alta)
      (pressao media)
    )
    and(
      (temperatura media)
      (pressao alta)
    )
  )
=>
  (assert (Tempo rapido))
)
</pre>

<pre>
(defrule cozimento-moderado
  (declare (salience 10))
  (or 
    and(
      (temperatura alta)
      (pressao baixa)
    )
    and(
      (temperatura media)
      (pressao media)
    )
    and(
      (temperatura baixa)
      (pressao alta)
    )
  )
=>
  (assert (Tempo moderado))
)
</pre>
