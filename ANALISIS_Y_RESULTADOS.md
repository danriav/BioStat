Reporte Analítico: Motor Predictivo ENSANUT
Este documento detalla la metodología, las decisiones de ingeniería de datos y los descubrimientos clínico-estadísticos obtenidos al procesar la base de datos de la Encuesta Nacional de Salud y Nutrición (ENSANUT) para predecir el riesgo de hipertensión arterial.

1. El Desafío de los Datos Crudos (Fase ETL)Los datasets gubernamentales y médicos del mundo real presentan un alto grado de entropía. Al realizar la exploración inicial (EDA) sobre los 4,363 registros, el sistema reportó erróneamente "0 valores nulos". Sin embargo, una auditoría profunda reveló trampas de datos (Data Traps):

    * Anomalías de Captura: Se detectaron 965 pacientes con un peso registrado menor a 30 kg (incluyendo adultos con registros de 2.0 kg) y 1,320 registros con una medida de cintura de 0.0 cm.
    * La Solución Estructural: En lugar de procesar estos datos irreales o eliminar el 30% del dataset, se implementó una estrategia de Destrucción Controlada y Rescate:
        1. Se forzaron las anomalías a valores nulos reales (NaN).
        2. Se rescataron datos utilizando columnas de redundancia (ej. segundamedicion_peso).
        3. Para los vacíos restantes, se descartó la imputación por promedio global (que destruye la varianza) y se implementó un algoritmo K-Nearest Neighbors (KNN). Esto permitió predecir la cintura o peso faltante de un paciente basándose en el promedio de sus 5 "gemelos estadísticos" más cercanos (por edad, sexo y estatura).

2. Ingeniería de Características (Feature Engineering)
La precisión de un modelo no depende solo del algoritmo, sino de la calidad de las variables que se le entregan. Se decidió no depender exclusivamente de las métricas planas del dataset, sintetizando biomarcadores compuestos que reflejan el estrés biomecánico y metabólico real:

Se utilizaron Biocarmadores sinteizados (IMC Recalculado, índice Cintura-Estatatura (ICT) e índice TyG) y su respectiva lógica Analítica y Fisiológica:

    - IMC Recalculado: Se recalculó el Índice de Masa Corporal desde cero para corregir inconsistencias en los registros originales de masa corporal del dataset.  
                                
    - Índice Cintura-Talla (ICT): (Cintura / Estatura). Se calculó para obtener un proxy de la distribución de grasa visceral (abdominal), la cual es metabólicamente activa y un factor de riesgo cardiovascular superior a la grasa subcutánea.

    - Índice TyG: Se aplicó una transformación logarítmica sobre la relación de Triglicéridos y Glucosa (ln[triglicéridos * glucosa / 2]). Este es un biomarcador científicamente validado para detectar resistencia a la insulina oculta.
    
3. Resultados del Motor Predictivo
Para la fase de inferencia, se aisló intencionalmente la variable de tension_arterial directa para evitar la fuga de datos (Data Leakage). El objetivo fue forzar a un algoritmo Random Forest Classifier (100 estimadores, pesos balanceados) a predecir la hipertensión basándose exclusivamente en factores estructurales, de estilo de vida y bioquímica básica.

Auditoría de Variables (Feature Importance)

Al extraer la lógica de decisión del algoritmo, los resultados confirmaron matemáticamente la jerarquía de riesgo fisiológico a nivel poblacional. El Top 4 de variables desencadenantes de hipertensión fue:

    1. Edad: El factor de mayor peso predictivo, representando el endurecimiento vascular natural y el tiempo bajo tensión del sistema endotelial.
    2. IMC Calculado: Posicionado en segundo lugar, demostrando que la carga de masa bruta del sistema (que obliga al corazón a bombear a través de una red capilar más extensa) es un predictor primario.
    3. Actividad total: El algoritmo determinó que el volumen de movimiento y entrenamiento actúa como el principal mecanismo compensatorio del sistema cardiovascular, superando el peso predictivo de exámenes clínicos como el colesterol o la insulina.
    4. Índice Cintura-Talla: El algoritmo determinó que el lugar donde se almacena la masa ofrece una señal analítica poderosa para predecir el colapso metabólico.

4. Conclusión y Escalabilidad Arquitectónica
El pipeline logró transformar un dataset ruidoso en un motor de inferencia robusto y explicable.
A nivel de ingeniería de software, el modelo resultante ha sido serializado (.pkl) desacoplándolo del entorno de análisis de datos. Esta abstracción permite que el algoritmo funcione como el "cerebro" de un microservicio independiente (por ejemplo, en FastAPI), listo para ingerir JSONs con datos reactivos de usuarios en tiempo real, evaluar el riesgo en milisegundos y detonar reglas de negocio o protocolos de optimización física en bases de datos relacionales de forma automatizada.