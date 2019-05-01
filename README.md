# Fuzzy - Panela-de-Pressao
### Trabalho - IA

### Implementação de um Sistema Especialista Fuzzy para o controle da velocidade de cozimento de uma Panela-de-Pressão
##### Acadêmicos: Charle Gonzaga, Cássio Rasveiler.

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
Para cada variável lingüística foi definido um template. No caso do template *temperatura*, utilizou-se
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

###### *A figura 1 ilustra os valores numéricos possíveis para a temperatura, considerando as variáveis lingüísticas*
###### *definidas na Tabela 1 e no template respectivo.*

#### 4. Conclusão
