# Experimento Colaborativo #01 · Catastrophe Analysis · Grupo B

**Karen Micaela García Ferrari** · Laboratorio de Implementación I · Modalidad Gerencial · UAustral 2026 Virtual

## Pregunta
¿Cuál es el mejor método para corregir las variables que en 202006 quedaron en cero por una falla del datawarehouse?

## Métodos comparados (todo lo demás del workflow igual, *ceteris paribus*)
| Método | Qué hace con las 12 variables rotas de 202006 |
|---|---|
| `asignar_NA` | Las pone en NA (método del workflow de la cátedra) |
| `dejar_ceros` | No las corrige |
| `interpolar` | Promedio del mismo cliente entre 202005 y 202007 (NA si falta alguno de los dos meses) |

## Diseño
* Workflow base: `z611_WorkFlow_01_gerencial_grupoB` de la cátedra, sin otros cambios.
* Futuro: 202107 (clase conocida). Final train: 202005–202105. Validación del Grid Search: 202105.
* Hiperparámetros: Grid Search (num_leaves × min_data_in_leaf) en la corrida `asignar_NA`; mejores: num_leaves = 256, min_data_in_leaf = 64, num_iterations = 399. Se reutilizan en los tres métodos.
* 8 semillas por método, las mismas para los tres (comparación pareada): 151729, 235877, 526777, 582037, 803273, 208057, 453029, 352493.
* Métrica: ganancia suavizada máxima en 202107 (millones; ventana de 400 envíos).
* Comparación: test de Wilcoxon pareado de cada método contra `asignar_NA`.

## Resultados (8 semillas por método)
| Método | Ganancia media (M) | Desvío | vs asignar_NA |
|---|---|---|---|
| asignar_NA | 32,74 | 1,29 | — |
| dejar_ceros | 33,30 | 1,04 | +0,56 · gana en 4 de 8 · p = 0,547 |
| interpolar | 33,70 | 1,07 | +0,96 · gana en 6 de 8 · p = 0,195 |

## Archivos
| Archivo | Contenido |
|---|---|
| `z611_CA_grupoB_GarciaFerrari.ipynb` | Notebook del experimento (el método se elige en la celda PARAMETROS) |
| `Corrida1_asignar_NA.ipynb` | Corrida 1 con salidas (incluye el Grid Search) |
| `Corrida2_dejar_ceros.ipynb` | Corrida 2 con salidas |
| `Corrida3_interpolar.ipynb` | Corrida 3 con salidas y análisis final |
| `resultados_CA.txt` | Ganancia y envíos de cada método y semilla |
| `CA_mejores_hiperparametros.txt` | Hiperparámetros elegidos en la corrida 1 |

## Cómo reproducirlo
1. Abrir `z611_CA_grupoB_GarciaFerrari.ipynb` en Google Colab.
2. Runtime Python 3: correr las celdas de montaje de Drive y descarga del dataset `gerencial_competencia_2026.csv.gz`.
3. Runtime R: en PARAMETROS, corrida 1 con `metodo <- "asignar_NA"` y `gridsearch <- TRUE`; corridas 2 y 3 con `"dejar_ceros"` e `"interpolar"` y `gridsearch <- FALSE`. Ejecutar desde "Inicializacion" hasta el final.
4. La última celda (Análisis) arma la tabla, el test de Wilcoxon y el gráfico a partir de `resultados_CA.txt`.
