# VizDoom DQN - Proyecto Final TAM

Proyecto final de la materia **Teoria de Aprendizaje de Maquina** del programa de pregrado en **Ingenieria Electronica**. El proyecto entrena y evalua un agente **Deep Q-Network (DQN)** para control visual en el entorno `VizdoomDefendCenter-v1`.

**Autores**

- Javier Estiven Leon Calderon - javleonca@unal.edu.co
- Laura Daniela Barragan Silva - lbarragans@unal.edu.co

**Notebook principal:** [notebooks/VizDoom_DQN_ProyectoFinal_TAM_2026_1.ipynb](notebooks/VizDoom_DQN_ProyectoFinal_TAM_2026_1.ipynb)

## Abstract

This repository presents an academic reinforcement learning project based on Deep Q-Networks for visual control in ViZDoom. The agent receives raw game frames, preprocesses them into grayscale stacked observations, and learns a policy that is evaluated against a random baseline. The project emphasizes reproducibility, quantitative evaluation, and a critical discussion of classical DQN limitations.

## Planteamiento

El entorno `VizdoomDefendCenter-v1` se modela como un proceso de decision de Markov:

```text
M = (S, A, P, R, gamma)
```

El agente observa imagenes del juego, selecciona una accion discreta y recibe una recompensa. Como una sola imagen no contiene informacion temporal suficiente, el estado se aproxima mediante una pila de frames consecutivos.

## Pipeline

1. **Entorno:** `VizdoomDefendCenter-v1` mediante Gymnasium y ViZDoom.
2. **Preprocesamiento visual:** extraccion de pantalla, escala de grises, redimensionamiento a `84 x 84`.
3. **Representacion de estado:** apilamiento de `4` frames en formato `(4, 84, 84)`.
4. **Agente:** `DQN` de Stable-Baselines3 con `CnnPolicy`.
5. **Entrenamiento:** tres semillas en modo `full`, cada una con `1,200,000` timesteps.
6. **Seleccion del modelo:** se usa `best_model.zip` segun evaluacion periodica.
7. **Evaluacion final:** comparacion cuantitativa contra una politica aleatoria en episodios independientes.
8. **Ablacion:** comparacion entre `frame_stack=1` y `frame_stack=4`.

## Configuracion experimental

| Parametro | Valor |
| --- | ---: |
| Entorno | `VizdoomDefendCenter-v1` |
| Politica | `CnnPolicy` |
| Imagen | `84 x 84`, escala de grises |
| Frame stack | `4` |
| Gamma | `0.99` |
| Learning rate | `1e-4` |
| Replay buffer | `100,000` |
| Batch size | `32` |
| Timesteps por semilla | `1,200,000` |
| Semillas | `42`, `123`, `2026` |
| Episodios de evaluacion final | `50` por politica y semilla |

## Resultados principales

Evaluacion final usando el mejor checkpoint de cada semilla:

| Politica | Seed | Recompensa media | Desv. est. | Min | Max | Steps medios | Kills medios |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| DQN_best | 42 | 20.18 | 3.85 | 11.00 | 25.00 | 988.46 | 21.18 |
| DQN_best | 123 | 20.22 | 3.64 | 5.00 | 25.00 | 1009.68 | 21.22 |
| DQN_best | 2026 | 17.24 | 3.97 | 8.00 | 25.00 | 907.06 | 18.24 |
| Random | 42 | 0.10 | 0.65 | -1.00 | 2.00 | 305.08 | 1.10 |
| Random | 123 | -0.06 | 0.79 | -1.00 | 2.00 | 290.60 | 0.94 |
| Random | 2026 | 0.22 | 0.82 | -1.00 | 2.00 | 323.72 | 1.22 |

Los resultados muestran que DQN aprende una politica claramente superior a la politica aleatoria para esta tarea especifica de defensa en ViZDoom.

## Ablacion: apilamiento temporal

| Politica | Seed | Episodios | Recompensa media | Desv. est. | Steps medios | Kills medios |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| DQN_stack_1 | 901 | 10 | 3.40 | 1.35 | 368.60 | 4.40 |
| DQN_stack_4 | 904 | 10 | 8.60 | 3.20 | 729.30 | 9.60 |

La ablacion sugiere que el apilamiento de frames ayuda al agente a capturar informacion temporal relevante, como direccion y movimiento de enemigos.

## Uso

### Opcion recomendada: Google Colab

1. Abrir el notebook principal desde GitHub o subirlo a Colab.
2. Ejecutar las celdas en orden.
3. Para una prueba rapida, cambiar:

```python
RUN_MODE = "quick"
```

4. Para reproducir el experimento completo, usar:

```python
RUN_MODE = "full"
```

El modo `full` puede tardar varias horas y requiere GPU para una ejecucion practica.

### Ejecucion local

Crear un entorno virtual e instalar dependencias:

```powershell
python -m venv .venv
.\.venv\Scripts\activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

Luego abrir el notebook:

```powershell
jupyter notebook notebooks/VizDoom_DQN_ProyectoFinal_TAM_2026_1.ipynb
```

En Windows, ViZDoom puede requerir configuracion adicional de dependencias graficas/audio. Por eso Colab es la opcion mas robusta para reproducir la entrega.

## Artefactos no incluidos

Este repositorio no incluye modelos entrenados, videos, replay buffers, logs ni checkpoints pesados. Esos archivos son generables a partir del notebook y se excluyen para mantener el repositorio liviano y apto para GitHub.

## Limitaciones

- Se usa DQN clasico, sin Double DQN, Dueling DQN ni Prioritized Experience Replay.
- El aprendizaje por refuerzo presenta alta varianza entre episodios y semillas.
- El agente aprende una politica efectiva para `VizdoomDefendCenter-v1`, no una solucion general para Doom.
- El video cualitativo es evidencia complementaria; la evidencia principal es la evaluacion cuantitativa.

## Referencias

- Mnih et al. (2015). *Human-level control through deep reinforcement learning*. Nature.
- Mnih et al. (2013). *Playing Atari with Deep Reinforcement Learning*. arXiv.
- Kempka et al. (2016). *ViZDoom: A Doom-based AI Research Platform for Visual Reinforcement Learning*. arXiv.
- Stable-Baselines3 documentation.
- Gymnasium documentation.
