# QSVM vs SVM — Protocolo Reproducible para Comparación de Algoritmos de Aprendizaje Cuántico

## 📋 Descripción General

Este notebook implementa un **protocolo robusto y reproducible** para comparar **QSVM (Support Vector Machine Cuántico)** versus **SVM clásico (Support Vector Machine)** usando validación cruzada estratificada con múltiples repeticiones, búsqueda de hiperparámetros y pruebas estadísticas rigurosas.

### Características principales:
- ✅ **Validación cruzada repetida** (k-fold estratificado con múltiples semillas)
- ✅ **Grid search** de hiperparámetros compatible para ambos modelos
- ✅ **Pruebas estadísticas** (t pareada, Wilcoxon) con tamaños de efecto
- ✅ **Reproducibilidad garantizada** mediante semillas globales
- ✅ **Exportación de resultados** en CSV y JSON para auditoría
- ✅ **Alineación de kernels** (justificación de kernel cuántico vs clásicos)
- ✅ **Visualizaciones** de resultados y comparativas

---

## 🔧 Requisitos e Instalación

### Versiones Recomendadas
```bash
# Python 3.13.13
python --version

# Instalar dependencias (ejecutar una sola vez)
pip install --upgrade pip

# Paquetes principales
pip install \
    qiskit==1.4.4 \
    qiskit-machine-learning==0.8.4 \
    scikit-learn \
    scipy \
    pandas \
    matplotlib \
    numpy \
    qiskit_algorithms \
    qiskit_aer

# Opcional: Si usarás hardware cuántico real de IBM
pip install qiskit-ibm-runtime==0.23.0
```

### Verificar Instalación
```python
import qiskit
import qiskit_machine_learning
import sklearn
print(f"Qiskit: {qiskit.__version__}")
print(f"Qiskit ML: {qiskit_machine_learning.__version__}")
print(f"Scikit-learn: {sklearn.__version__}")
```
---

## 📊 Paso a Paso para Usar el Notebook

### **Paso 1: Ejecutar Requisitos e Instalación (Celda 0)**
```python
# Descomentar y ejecutar si es la primera vez
# version python 3.13.13
# !pip install --upgrade pip
# !pip install qiskit==1.4.4 qiskit-machine-learning==0.8.4 scikit-learn scipy pandas matplotlib qiskit_algorithms qiskit_aer
# Opcional: IBM Runtime (si usarás hardware real)
# !pip install qiskit-ibm-runtime==0.23.0
```
**Acción**: Ejecuta esta celda solo si no tienes las dependencias instaladas.

---

### **Paso 2: Configuración y Reproducibilidad (Celda 1)**

**Qué hace**:
- Define la semilla global para reproducibilidad
- Crea carpeta de salida `results_qsvm_experiment/`
- **Importante**: Todos los resultados se guardarán aquí

**Parámetros ajustables**:
- `SEED`: Cambia para diferentes ejecutiones (default: 42)
- `OUT_DIR`: Ruta donde guardar resultados (default: "./results_qsvm_experiment")

---

### **Paso 3: Carga de Datos (Celdas 2-4)**
**Dataset**: Breast Cancer Wisconsin (scikit-learn)
- **569 muestras**, **30 features**, **2 clases** (binaria)

**Transformaciones automáticas**:
1. **Estandarización**: Escala las features a media 0, varianza 1
2. **Reducción PCA** (automática): Si el dataset tiene más features que qubits disponibles (máx. 10), aplica PCA


**💡 Tip**: Para usar otro dataset, reemplaza `load_breast_cancer()` con tu dataset personalizado.

---

### **Paso 4: Protocolo de Validación (Celda 5)**
Define la estrategia de validación cruzada:

**Parámetros recomendados**:
- Para test rápido: `N_SPLITS=2, N_REPEATS=2` (4 evaluaciones)
- Para producción: `N_SPLITS=5, N_REPEATS=10` (50 evaluaciones)
- Para rigor máximo: `N_SPLITS=10, N_REPEATS=10` (100 evaluaciones)

---

### **Paso 5: Modelos Clásicos - SVM (Celdas 6)**
Configura y busca hiperparámetros para SVM clásico:

**Parámetros a ajustar**:
- `C`: Regularización (menor = más regularizado, default: [0.1, 1, 10, 100])
- `gamma`: Curvatura del kernel RBF (default: [0.01, 0.1, 1, "scale"])
- `kernel`: Tipo de kernel (default: rbf, linear, poly)
- `degree`: Para kernel polinomial (default: [2, 3])

---

### **Paso 6: QSVM - Kernel Cuántico (Celdas 7)**
Configura Support Vector Machine con kernel cuántico:

**Parámetros clave**:
- `reps`: Profundidad del circuito cuántico (default: [1, 2, 3])
  - Más reps = circuito más profundo = mayor capacidad (pero mayor ruido)
- `entanglement`: Tipo de entrelazamiento (default: "linear")
- `feature_dimension`: Dimensión de features (auto-ajustado)

**Nota**: Se usa `StatevectorSampler()` para simulación local.

**Nota**: Usar `(Celda 7.2)` para hardware real de IBM.
**Parámetros clave**:
- `USE_IBM_RUNTIME` = Cambia a True si tienes IBM Runtime configurado.
- `IBM_CHANNEL` = usar "ibm_quantum" o "ibm_cloud"; requiere autenticación previa con Cloud IBM.

---

### **Paso 7: Bucle de Evaluación (Celda 8)**
Ejecuta la validación cruzada para ambos modelos:

**⏱️ Tiempo de ejecución**: Depende de configuración, típicamente 5-10 horas.

---

### **Paso 8: Estadística Inferencial (Celda 9)**
Compara significancia estadística entre modelos:

**Interpretación**:
- **p-value < 0.05**: Diferencia estadísticamente significativa
- **Cohen's d**:
  - 0.2 = efecto pequeño
  - 0.5 = efecto medio
  - 0.8+ = efecto grande

---

### **Paso 9: Alineación de Kernels (Celda 10)**
Justifica la elección del kernel cuántico comparándolo con kernels clásicos:

**Resultado típico**:
- Si correlación RBF vs QSVM > 0.8: El kernel cuántico aproxima comportamiento RBF
- Si correlación es baja: El kernel cuántico tiene ventaja diferenciadora

---

### **Paso 10: Visualizaciones (Celda 11)**
Genera gráficos de comparación.

---

### **Paso 11: Resumen y Exportación (Celda 12)**
Guarda todos los resultados.
