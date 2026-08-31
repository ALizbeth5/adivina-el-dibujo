#  Adivina mi Dibujo

**Proyecto Final Individual — Inteligencia Artificial**

Sistema de clasificación de dibujos en tiempo real: mientras el usuario dibuja con el mouse, una red neuronal convolucional (CNN) predice a cuál de 5 categorías pertenece el trazo, actualizando la predicción de forma continua sin necesidad de presionar ningún botón.

![Python](https://img.shields.io/badge/Python-3.x-blue)
![TensorFlow](https://img.shields.io/badge/TensorFlow-Keras-orange)
![Gradio](https://img.shields.io/badge/Interfaz-Gradio-purple)
![Accuracy](https://img.shields.io/badge/Accuracy-96.58%25-brightgreen)

---

##  Tabla de contenido

- [Descripción](#-descripción)
- [Demo](#-demo)
- [Categorías](#-categorías)
- [Dataset](#-dataset)
- [Arquitectura del modelo](#-arquitectura-del-modelo)
- [Resultados](#-resultados)
- [Estructura del repositorio](#-estructura-del-repositorio)
- [Cómo ejecutarlo](#-cómo-ejecutarlo)
- [Decisiones de diseño](#-decisiones-de-diseño)
- [Autora](#-autora)

##  Descripción

El modelo es una **Red Neuronal Convolucional entrenada desde cero** (sin Transfer Learning, ya que los datos son trazos simplificados y no fotografías) con el dataset [Quick, Draw!](https://quickdraw.withgoogle.com/data) de Google. La interfaz está construida con [Gradio](https://www.gradio.app/), usando un lienzo de dibujo (`Sketchpad`) que envía la predicción en tiempo real (`live=True`) conforme el usuario dibuja.


## 🗂️ Dataset

- **Fuente:** [Quick, Draw! Dataset](https://quickdraw.withgoogle.com/data) (Google, dominio público)
- **Tamaño total:** 10,000 imágenes (28×28, escala de grises)
- **División:** 80% entrenamiento (8,000) / 20% prueba (2,000), mezcladas aleatoriamente (`seed=42`) antes de dividir
- **Preprocesamiento:** normalización de píxeles de [0, 255] a [0, 1]

## 🧠 Arquitectura del modelo

```
Input (28×28×1)
   ↓
Conv2D(32, 3×3, relu) → MaxPooling2D(2×2)
   ↓
Conv2D(64, 3×3, relu) → MaxPooling2D(2×2)
   ↓
Flatten → Dense(128, relu) → Dropout(0.3)
   ↓
Dense(5, softmax)
```

- **Optimizador:** Adam
- **Función de pérdida:** sparse_categorical_crossentropy
- **Épocas:** 15, con 10% de validación

## 📊 Resultados

| Métrica | Valor |
|---|---|
| Accuracy (test) | **96.58%** |
| Loss (test) | 0.1549 |
| Meta mínima del proyecto | 75% ✅ |

**Matriz de confusión (conjunto de prueba):**

| Real \ Predicción | Gato | Casa | Sol | Bicicleta | Paraguas |
|---|---|---|---|---|---|
| **Gato** | 386 | 1 | 7 | 3 | 3 |
| **Casa** | 10 | 370 | 0 | 0 | 1 |
| **Sol** | 5 | 2 | 385 | 2 | 3 |
| **Bicicleta** | 8 | 1 | 1 | 409 | 0 |
| **Paraguas** | 2 | 1 | 8 | 2 | 388 |

Las confusiones más frecuentes fueron **sol ↔ paraguas** y **gato ↔ sol**: en trazos rápidos y esquemáticos, ambas comparten una forma curva/circular base con líneas saliendo (rayos, varillas, orejas), lo que dificulta su distinción.

##  Estructura del repositorio

```
├── adivina_mi_dibujo.ipynb   # Notebook completo: dataset, entrenamiento, app
├── adivina_mi_dibujo.h5      # Modelo entrenado exportado
├── informe_tecnico.docx      # Informe técnico del proyecto
└── README.md
```

## Cómo ejecutarlo

1. Abre `adivina_mi_dibujo.ipynb` en [Google Colab](https://colab.research.google.com/).
2. Ejecuta todas las celdas en orden (descarga del dataset → entrenamiento → app de Gradio).
   - Para probar solo la app sin re-entrenar, carga el modelo directamente:
     ```python
     modelo = tf.keras.models.load_model('adivina_mi_dibujo.h5')
     ```
3. Al ejecutar `interfaz.launch(share=True)`, Gradio genera un link público temporal — ábrelo para dibujar y ver la predicción actualizarse en tiempo real.

**Requisitos:** `tensorflow`, `numpy`, `matplotlib`, `scikit-learn`, `seaborn`, `gradio`, `Pillow` (todo preinstalado en Colab, excepto `gradio`, que se instala con `!pip install gradio -q`).

##  Decisiones de diseño

- Se limitó el dataset a 2,000 imágenes por clase para mantenerlo balanceado y con un tiempo de entrenamiento manejable en Colab.
- Se usó `Dropout(0.3)` para reducir el riesgo de sobreajuste dado el tamaño moderado del dataset.
- La app usa `live=True` en Gradio para actualizar la predicción automáticamente mientras se dibuja, sin botón de envío.
- Fue necesario **invertir los colores** de la imagen capturada por el `Sketchpad` (trazo oscuro sobre fondo claro) antes de pasarla al modelo, ya que Quick Draw representa sus imágenes al revés (trazo blanco sobre fondo negro).

##  Autora

**Lizbeth Chacasaguay** — Proyecto Final Individual, materia de Inteligencia Artificial
