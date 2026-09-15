# EP1 – RAG para Diagnóstico FTTH

**Evaluación Parcial N°1 – ISY0101 Ingeniería de Soluciones con Inteligencia Artificial**  
**Estudiante:** Miguel Zavala Vásquez

## Descripción

Este repositorio contiene un prototipo de asistente inteligente basado en **LLM + RAG (Retrieval-Augmented Generation)** para apoyar el análisis inicial de incidencias en redes de fibra óptica FTTH.

El sistema recupera antecedentes históricos relacionados semánticamente con una nueva incidencia y los complementa con documentación técnica externa. Con este contexto, un modelo de lenguaje genera una orientación técnica con trazabilidad de las fuentes utilizadas.

> El prototipo es una herramienta de apoyo. La validación, el diagnóstico y la decisión final corresponden al personal técnico.

## Motivación del proyecto

La elección de este proyecto nace de mi experiencia laboral como supervisor de fibra óptica en el área de mantenimiento de redes para Claro y VTR. Dentro de mis funciones habituales participo en la gestión y supervisión de reparaciones de distintos tipos de enlaces de fibra óptica, entre ellos redes FTTH (*Fiber to the Home* o *fibra hasta el hogar*).

Debido a mi interés por esta área y a la experiencia adquirida en terreno, decidí orientar lo aprendido en la asignatura hacia una problemática relacionada directamente con mi trabajo. La idea fue desarrollar un prototipo que pudiera utilizar antecedentes de incidencias FTTH anteriores como apoyo para mis tareas de supervisión, facilitando la búsqueda de información y entregando una orientación inicial antes y durante el proceso de diagnóstico de una falla.

## Problema abordado

En la atención de una incidencia FTTH se requiere identificar la infraestructura asociada, realizar mediciones, consultar planimetría y orientar las revisiones en terreno. Además, puede ser necesario revisar manualmente antecedentes de fallas anteriores similares.

Este prototipo se enfoca en esta última etapa: facilitar la recuperación de experiencias históricas y documentación técnica que puedan servir como antecedente antes o durante el diagnóstico.

## Datos utilizados

La base de trabajo contiene **1.411 incidencias FTTH anonimizadas**. Para el prototipo se seleccionaron cuatro categorías:

| Categoría | Casos disponibles | Casos utilizados en el MVP |
|---|---:|---:|
| Filamento cortado | 180 | 20 |
| Atenuación | 167 | 20 |
| Caja CTO en mal estado | 83 | 20 |
| Filamento Atenuado | 75 | 20 |
| **Total** | **505** | **80** |

Por privacidad, la base operacional original no se publica en este repositorio. Los documentos del prototipo utilizan identificadores anonimizados del tipo `FTTH-XXXX`.

## Arquitectura

El flujo principal implementado es:

`Nueva incidencia → Embedding → Chroma → Recuperación de contexto → Gemini → Respuesta técnica trazable`

El contexto recuperado combina:

- **Fuente interna:** casos históricos FTTH anonimizados.
- **Fuente externa:** referencia técnica basada en ITU-T G.984.1 para arquitectura GPON.

Los documentos se distinguen mediante metadatos como `fuente_interna` y `fuente_externa`.

## Tecnologías utilizadas

- Python
- Google Colab
- Pandas
- Matplotlib
- LangChain
- Google Generative AI
- `gemini-embedding-001`
- Chroma
- Gemini como LLM

## Funcionamiento del RAG

1. Se prepara cada incidencia histórica como un documento de conocimiento.
2. Los documentos se convierten en embeddings.
3. Los embeddings se indexan en Chroma.
4. Una nueva incidencia se transforma también en embedding.
5. Chroma recupera antecedentes semánticamente relacionados.
6. Se recupera además documentación técnica externa cuando corresponde.
7. El contexto recuperado se entrega al LLM mediante un prompt con reglas de control.
8. El modelo genera análisis, antecedentes, posible orientación, verificaciones sugeridas y trazabilidad.

## Controles del prompt

El prompt utilizado indica al modelo que:

- no invente mediciones, causas ni reparaciones;
- no presente una posible causa como diagnóstico confirmado;
- diferencie fuentes internas y externas;
- proponga verificaciones basándose en el contexto recuperado;
- identifique los `Caso_ID` utilizados;
- identifique la referencia externa cuando sea utilizada;
- mantenga la decisión final en el personal técnico.

## Ejecución

El prototipo fue desarrollado en Google Colab.

1. Abrir `EP1_RAG_Diagnostico_FTTH_FINAL.ipynb` en Google Colab.
2. Configurar `GOOGLE_API_KEY` en **Secrets** de Colab.
3. Habilitar el acceso del notebook al Secret.
4. Ejecutar las celdas en orden.
5. Para reproducir completamente el análisis se requiere una base FTTH con la estructura esperada. La base operacional utilizada en la evaluación no se publica por contener información de uso interno.

**Importante:** la API key no debe escribirse ni publicarse directamente en el código.

## Pruebas realizadas

Se probaron, entre otros, dos escenarios:

- degradación de potencia óptica cercana a **-25 dBm**, recuperando históricos relacionados con atenuación;
- daño físico en una **caja CTO**, recuperando casos históricos de la misma categoría.

En la prueba combinada se recuperaron **3 antecedentes internos + 1 fuente externa**, y la respuesta final identificó explícitamente las fuentes utilizadas.

## Alcance y limitaciones

- El MVP utiliza 80 históricos de los 505 disponibles en las categorías seleccionadas.
- La fuente externa incorporada corresponde a una síntesis técnica de ITU-T G.984.1.
- El prototipo no identifica automáticamente OLT, tarjeta, puerto, nodo, NAP, anillo o sitio técnico desde una dirección.
- No reemplaza mediciones OTDR, planimetría ni validación en terreno.
- La respuesta generada debe ser revisada por personal técnico.

## Proyección futura

Una evolución posible es integrar inventario y datos de planta para asociar una dirección o servicio con su infraestructura FTTH antes de ejecutar el proceso RAG.

## Privacidad y seguridad

No se publican datos de clientes, direcciones, nombres de trabajadores, números telefónicos ni la base operacional original. Tampoco se incluyen claves API o credenciales.

## Referencia técnica

International Telecommunication Union. (2008). *ITU-T Recommendation G.984.1: Gigabit-capable passive optical networks (GPON): General characteristics*.

## Uso de IA

Durante el desarrollo se utilizaron herramientas de IA como apoyo para comprender conceptos, depurar código, revisar redacción y estructurar material técnico. Los resultados del prototipo fueron comprobados mediante ejecuciones en Google Colab y contrastados con los antecedentes utilizados.
