# Algoritmo FOIL - Implementación en Python

**Alumna:** Angeles Mansilla
**Profesora:** Yanina Scudero
**Materia:** Procesamiento de Aprendizaje Automático
**Tema:** Algoritmo FOIL (First Order Inductive Learner)

---

## Descripción

Este repositorio contiene una implementación simplificada del **Algoritmo FOIL** (First Order Inductive Learner) en Python, junto con scripts para calcular la **Ganancia de Información (FOIL Gain)** sobre un dataset de ejemplo.

> **Nota:** Las respuestas a los ejercicios, los cálculos manuales y las interpretaciones se encuentran en el archivo `Informe.pdf`.

---

## ¿Qué es FOIL?

FOIL (First Order Inductive Learner) es un algoritmo de aprendizaje inductivo diseñado por J. Ross Quinlan (1990) que genera **reglas lógicas de primer orden** a partir de:

- **Ejemplos positivos** (los que cumplen el concepto).
- **Ejemplos negativos** (los que no lo cumplen).
- **Conocimiento previo** (predicados disponibles).
- **Un objetivo** (el predicado a aprender, ej: `en_formacion(X)`).

FOIL construye reglas agregando condiciones que maximizan la Ganancia de Información (FOIL Gain), hasta cubrir todos los positivos y excluir los negativos.

### Fórmula del FOIL Gain

```text
FOIL Gain = p × ( log2(p / (p + n)) - log2(P / (P + N)) )
```

Donde:

- **P** y **N**: positivos y negativos *antes* de agregar la condición.
- **p** y **n**: positivos y negativos *después* de agregar la condición.

---

## Estructura del Repositorio

```text
📁 algoritmo-foil/
├── 📄 README.md                    ← Este archivo (explicación y códigos)
├── 📄 codigo_FOIL.py               ← Código 1: Inducción de reglas
├── 📄 codigo_FOIL_condicion3.py    ← Código 2: FOIL Gain para nivel_educativo
├── 📄 codigo_FOIL_edad.py          ← Código 3: FOIL Gain para edad
└── 📄 Informe.pdf                  ← Ejercicios resueltos (cálculos e interpretaciones)
```

---

## Códigos

### `codigo_FOIL.py`

Induce la regla lógica que describe el concepto de "persona en formación" a partir del dataset.

```python
# Algoritmo FOIL simplificado: inducir condiciones que aparecen en positivos pero no en negativos
def inducir_regla(positivos, negativos):
    atributos = ["edad", "departamento", "nivel_educativo"]
    regla = {}

    for atributo in atributos:
        valores_pos = set(p[atributo] for p in positivos)
        valores_neg = set(p[atributo] for p in negativos)
 
        # Para edad, usamos valores numéricos, así que buscamos intersección mínima
        if atributo == "edad":
            valores_validos = [v for v in valores_pos if v not in valores_neg]
        else:
             valores_validos = list(valores_pos - valores_neg)

        if valores_validos:
            regla[atributo] = valores_validos

    return regla

# Programa Principal

# Simulación de un conjunto de datos realista con personas y sus atributos
datos = [
    {"edad": 22, "departamento": "IT", "nivel_educativo": "terciario", "en_formacion": True},
    {"edad": 24, "departamento": "IT", "nivel_educativo": "universitario", "en_formacion": True},
    {"edad": 21, "departamento": "RRHH", "nivel_educativo": "terciario", "en_formacion": True},
    {"edad": 35, "departamento": "IT", "nivel_educativo": "universitario", "en_formacion": False},
    {"edad": 40, "departamento": "Finanzas", "nivel_educativo": "maestría", "en_formacion": False},
    {"edad": 29, "departamento": "RRHH", "nivel_educativo": "universitario", "en_formacion": False},
    {"edad": 23, "departamento": "IT", "nivel_educativo": "terciario", "en_formacion": True},
    {"edad": 38, "departamento": "Finanzas", "nivel_educativo": "universitario", "en_formacion": False}
]

# Separar ejemplos positivos y negativos
positivos = [p for p in datos if p["en_formacion"]]
negativos = [p for p in datos if not p["en_formacion"]]

# Ejecutar el algoritmo
regla_inducida = inducir_regla(positivos, negativos)

# Mostrar la regla
print("Regla inducida para identificar a personas en formación:")
for atributo, valores in regla_inducida.items():
    print(f"- {atributo} debe ser uno de: {valores}")
```

### `codigo_FOIL_condicion3.py`

Calcula la Ganancia de Información para la condición `nivel_educativo == 'terciario'`.

```python
import math

# Cálculo FOIL Gain
def log2_safe(x):
    return math.log2(x) if x > 0 else float('-inf')

# Datos
datos = [
    {"edad": 22, "departamento": "IT", "nivel_educativo": "terciario", "en_formacion": True},
    {"edad": 24, "departamento": "IT", "nivel_educativo": "universitario", "en_formacion": True},
    {"edad": 21, "departamento": "RRHH", "nivel_educativo": "terciario", "en_formacion": True},
    {"edad": 35, "departamento": "IT", "nivel_educativo": "universitario", "en_formacion": False},
    {"edad": 40, "departamento": "Finanzas", "nivel_educativo": "maestría", "en_formacion": False},
    {"edad": 29, "departamento": "RRHH", "nivel_educativo": "universitario", "en_formacion": False},
    {"edad": 23, "departamento": "IT", "nivel_educativo": "terciario", "en_formacion": True},
    {"edad": 38, "departamento": "Finanzas", "nivel_educativo": "universitario", "en_formacion": False}
]

# Valores antes de aplicar la condición
P = sum(1 for d in datos if d["en_formacion"])
N = sum(1 for d in datos if not d["en_formacion"])

# Aplicar condición: nivel_educativo == "terciario"
filtrados = [d for d in datos if d["nivel_educativo"] == "terciario"]
p = sum(1 for d in filtrados if d["en_formacion"])
n = sum(1 for d in filtrados if not d["en_formacion"])

foil_gain = p * (log2_safe(p / (p + n)) - log2_safe(P / (P + N)))

# Mostrar resultados
print(f"P = {P}, N = {N}")
print(f"p = {p}, n = {n}")
print(f"p / (p + n) = {p / (p + n):.3f}")
print(f"P / (P + N) = {P / (P + N):.3f}")
print(f"log2(p / (p + n)) = {log2_safe(p / (p + n)):.3f}")
print(f"log2(P / (P + N)) = {log2_safe(P / (P + N)):.3f}")
print(f"FOIL Gain = {foil_gain:.3f}")
```

### `codigo_FOIL_edad.py`

Calcula la Ganancia de Información para la condición `edad <= 23`.

```python
import math

# Cálculo FOIL Gain
def log2_safe(x):
    return math.log2(x) if x > 0 else float('-inf')

# Datos
datos = [
    {"edad": 22, "departamento": "IT", "nivel_educativo": "terciario", "en_formacion": True},
    {"edad": 24, "departamento": "IT", "nivel_educativo": "universitario", "en_formacion": True},
    {"edad": 21, "departamento": "RRHH", "nivel_educativo": "terciario", "en_formacion": True},
    {"edad": 35, "departamento": "IT", "nivel_educativo": "universitario", "en_formacion": False},
    {"edad": 40, "departamento": "Finanzas", "nivel_educativo": "maestría", "en_formacion": False},
    {"edad": 29, "departamento": "RRHH", "nivel_educativo": "universitario", "en_formacion": False},
    {"edad": 23, "departamento": "IT", "nivel_educativo": "terciario", "en_formacion": True},
    {"edad": 38, "departamento": "Finanzas", "nivel_educativo": "universitario", "en_formacion": False}
]

# Valores antes de aplicar la condición
P = sum(1 for d in datos if d["en_formacion"])
N = sum(1 for d in datos if not d["en_formacion"])

# Aplicar condición: edad <= 23
filtrados = [d for d in datos if d["edad"] <= 23]
p = sum(1 for d in filtrados if d["en_formacion"])
n = sum(1 for d in filtrados if not d["en_formacion"])

foil_gain = p * (log2_safe(p / (p + n)) - log2_safe(P / (P + N)))

# Mostrar resultados
print(f"P = {P}, N = {N}")
print(f"p = {p}, n = {n}")
print(f"p / (p + n) = {p / (p + n):.3f}")
print(f"P / (P + N) = {P / (P + N):.3f}")
print(f"log2(p / (p + n)) = {log2_safe(p / (p + n)):.3f}")
print(f"log2(P / (P + N)) = {log2_safe(P / (P + N)):.3f}")
print(f"FOIL Gain = {foil_gain:.3f}")
```

---

## Cómo Ejecutar los Códigos

### Requisitos

- Python 3.8 o superior.
- No se requieren librerías externas. Solo se utiliza el módulo `math`, incluido en la biblioteca estándar de Python.

### Instrucciones

Abrí una terminal, navegá hasta la carpeta del repositorio y ejecutá:

```bash
python3 codigo_FOIL.py
python3 codigo_FOIL_condicion3.py
python3 codigo_FOIL_edad.py
```

### Salidas esperadas

**`codigo_FOIL.py`**

```text
Regla inducida para identificar a personas en formación:
- edad debe ser uno de: [24, 21, 22, 23]
- nivel_educativo debe ser uno de: ['terciario']
```

**`codigo_FOIL_condicion3.py`**

```text
P = 4, N = 4
p = 3, n = 0
p / (p + n) = 1.000
P / (P + N) = 0.500
log2(p / (p + n)) = 0.000
log2(P / (P + N)) = -1.000
FOIL Gain = 3.000
```

**`codigo_FOIL_edad.py`**

La salida es idéntica a la del script anterior, ya que ambas condiciones (`nivel_educativo == 'terciario'` y `edad <= 23`) cubren los mismos 3 positivos y ningún negativo, por lo que producen la misma ganancia.

---

## Referencias

- Quinlan, J. R. (1990). *Learning logical definitions from relations.* Machine Learning, 5(3), 239-266.
- Material de clase: Procesamiento de Aprendizaje Automático.
