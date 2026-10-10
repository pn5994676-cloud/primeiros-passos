# 📊 Dia 01 — Python para dados: tipos e estruturas

Primeiro dia da trilha de **Ciência de Dados**. O objetivo não é decorar sintaxe, e sim entender **como o Python representa dados** — porque toda análise começa por saber o que cada variável realmente é.

> **Pergunta que sustenta o dia:** como eu guardo, organizo e leio dados em Python?

---

## 🧠 Conceito

Python é uma linguagem de **tipos dinâmicos**: a variável não tem tipo fixo, o **valor** tem. Os tipos básicos que aparecem o tempo todo em dados são:

| Tipo | Exemplo | Uso típico |
|---|---|---|
| `int` | `42` | contagens, ids |
| `float` | `3.14` | médias, preços |
| `str` | `"São Paulo"` | texto, categorias |
| `bool` | `True` | filtros, condições |
| `None` | `None` | valor ausente |

Além dos tipos simples, a análise de dados usa **estruturas**:

- **`list`** — sequência ordenada e mutável: `[10, 20, 30]`
- **`tuple`** — sequência imutável: `(10, 20)`
- **`dict`** — pares chave→valor: `{"SP": 44_000_000}`
- **`set`** — coleção sem repetições: `{"SP", "RJ"}`

## 🔬 Prática

```python
# tipos básicos
idade = 30            # int
altura = 1.75         # float
nome = "Ana"          # str
ativo = True          # bool
ausente = None        # None

print(type(idade), type(altura), type(nome))
# <class 'int'> <class 'float'> <class 'str'>

# lista: sequência de valores
notas = [8.5, 7.0, 9.2, 6.8]

print(len(notas))      # 4  -> quantidade de itens
print(notas[0])        # 8.5 -> primeiro item (índice começa em 0)
print(notas[-1])       # 6.8 -> último item
print(sum(notas))      # 31.5

# média sem biblioteca
media = sum(notas) / len(notas)
print(round(media, 2))  # 7.88

# dicionário: dado com rótulo
populacao = {"SP": 44_400_000, "RJ": 16_000_000, "MG": 20_500_000}

for estado, valor in populacao.items():
    print(estado, valor)
```

## 🎯 Missão

Dado o dicionário abaixo, calcule e imprima a **média das notas** e o **nome do aluno com a maior nota**:

```python
alunos = {
    "Ana": 8.5,
    "Bruno": 7.0,
    "Carla": 9.2,
    "Diego": 6.8,
}
```

<details>
<summary>Ver solução</summary>

```python
alunos = {"Ana": 8.5, "Bruno": 7.0, "Carla": 9.2, "Diego": 6.8}

media = sum(alunos.values()) / len(alunos)
melhor = max(alunos, key=alunos.get)

print(f"Média: {media:.2f}")     # Média: 7.88
print(f"Maior nota: {melhor}")   # Maior nota: Carla
```

</details>

## ⚠️ Erro & correção

**Erro 1 — somar texto com número:**

```python
nota = "8.5"          # str, não float!
total = nota + 1      # TypeError: can only concatenate str (not "int") to str
```

Correção: converter o tipo com `float(nota)`.

**Erro 2 — esquecer que o índice começa em 0:**

```python
notas = [8.5, 7.0, 9.2]
print(notas[3])       # IndexError: list index out of range
```

O último índice válido é `len(notas) - 1` — ou use `notas[-1]`.

**Erro 3 — confundir `==` (comparação) com `=` (atribuição):**

```python
if nota = 8.5:        # SyntaxError
if nota == 8.5:       # correto
```

## 💡 Lição

Antes de analisar qualquer dado, **descubra o tipo dele**. A maior parte dos bugs em análise de dados nasce de um número que na verdade é texto, ou de um valor ausente (`None`/`NaN`) tratado como se fosse zero. Conferir tipos é o primeiro passo do trabalho — não uma etapa opcional.

---

**Próximo:** [Dia 02 — Controle de fluxo](roadmap-ciencia-de-dados.md) · **Trilha completa:** [`roadmap-ciencia-de-dados.md`](roadmap-ciencia-de-dados.md)
