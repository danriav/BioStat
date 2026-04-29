# BioStat MX: Motor Predictivo de Hipertensión Arterial 🩸

Este proyecto es un pipeline de Machine Learning de extremo a extremo diseñado para analizar datos antropométricos y bioquímicos de la población mexicana (dataset ENSANUT) y predecir el riesgo cardiovascular. 

El repositorio demuestra la aplicación de limpieza de datos rigurosa, síntesis de biomarcadores y modelado predictivo explicable, generando un motor estadístico listo para ser consumido por arquitecturas de microservicios.

## 🧠 Arquitectura y Flujo de Datos

1. **Curación de Datos (ETL):** Procesamiento de datos gubernamentales masivos. Detección de anomalías de captura (ej. pesos físicos imposibles) e imputación estadística multivariada utilizando el algoritmo **K-Nearest Neighbors (KNN)** para preservar la integridad de la varianza poblacional.
2. **Ingeniería de Características:** Creación de biomarcadores metabólicos a partir de medidas crudas:
   - **Índice Cintura-Talla (ICT):** Evaluador de riesgo de grasa visceral.
   - **Índice TyG:** Marcador logarítmico para resistencia a la insulina.
   - **IMC Corregido:** Recalculado analíticamente desde cero.
3. **Modelado Predictivo:** Entrenamiento de un algoritmo **Random Forest Classifier** optimizado para balance de clases.
4. **Auditoría del Modelo:** Extracción de *Feature Importance* para determinar las variables de mayor impacto fisiológico en el desarrollo de la enfermedad.

## 📊 Resultados y Descubrimientos Clave

A través de la auditoría del modelo, se demostró matemáticamente que los factores biomecánicos y estructurales superan a biomarcadores aislados en la predicción de riesgo poblacional. La jerarquía de riesgo arrojó que:
1. **Edad** (Desgaste del sistema endotelial)
2. **Carga Bruta** (IMC)
3. **Distribución de Grasa Visceral** (Índice Cintura-Talla)

*El modelo final ha sido serializado (`.pkl`) y está diseñado para ser desplegado como un microservicio de inferencia en Python (ej. FastAPI), capaz de recibir JSONs de perfiles de usuarios desde un frontend reactivo y devolver evaluaciones de riesgo en milisegundos.*

## 🛠️ Tecnologías Utilizadas
- **Lenguaje:** Python
- **Análisis y Manipulación:** Pandas, NumPy
- **Machine Learning:** Scikit-Learn, Joblib
- **Visualización:** Matplotlib, Seaborn

## 🚀 Instalación y Reproducción del Entorno

Para replicar el laboratorio de datos localmente:

```bash
# 1. Clonar el repositorio
git clone [https://github.com/danriav/BioStat_Predictor.git](https://github.com/tu-usuario/BioStat_Predictor.git)
cd BioStat_Predictor

# 2. Crear y activar entorno virtual
python -m venv venv
source venv/bin/activate  # En Windows: venv\Scripts\activate

# 3. Instalar dependencias
pip install -r requirements.txt