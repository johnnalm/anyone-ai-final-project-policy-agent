# Policy AI - Product Requirements Document (PRD)

## 1. Product Overview  
Policy AI es una **plataforma SaaS** que combina IA generativa, análisis de documentos y colaboración en tiempo real para simplificar la creación, análisis y modificación de pólizas de seguros.  

**Versión**: 1.0  
**Audiencia objetivo**: Aseguradoras, dueños de negocios, gestores de riesgos.  

---

## 2. Objetivos Principales  
1. Crear pólizas personalizadas mediante IA conversacional.  
2. Analizar y resumir pólizas existentes (PDF/Word).  
3. Modificar términos con interfaz visual de control de cambios.  
4. Integrar búsqueda web para respuestas con información actualizada.  
5. Ofrecer una **UI/UX Intuitiva y Amigable** para empresas.  

---

## 3. Funcionalidades Clave  

### 3.1 Chat de IA Conversacional  
- **Tres modos de interacción**:  
  - **Creación de póliza**: Guía al usuario mediante preguntas para generar pólizas.  
  - **Análisis de póliza**: Resumen de cláusulas, explicación de términos técnicos.  
  - **Modificación de póliza**: Sugiere ediciones (ej: "Aumentar cobertura a $2M").  
- **Agentes de LangChain**:  
  - Decide dinámicamente si buscar en la base de datos, en la web, o delegar a un humano.  

### 3.2 Editor de Documentos con Control de Cambios  
- **Interfaz visual**:  
  - **Panel izquierdo**: Editor de pólizas (estilo Word Online).  
  - **Panel derecho**: Chat de IA.  
  - **Cambios destacados**:  
    - Verde: Adiciones (con efecto de cursor en tiempo real).  
    - Rojo: Eliminaciones (tachado).  
  - **Historial de versiones**: Comparación de revisiones.  
- **Exportación**: PDF/Word con seguimiento de cambios.  

### 3.3 Requisitos de UI/UX Premium  
- **Dashboard**: Diseño minimalista con modo oscuro/claro.  
- **Colaboración en tiempo real**: Múltiples usuarios con permisos (Admin/Editor/Lector).  
- **White-label**: Personalización para aseguradoras.  
- **Accesibilidad**: Cumplimiento WCAG 2.1.  

---

## 4. Arquitectura Técnica  

### 4.1 Flujo de Datos  
1. **EDA y Preprocesamiento**:  
   - Scripts en Python (`PyPDF2`, `python-docx`, Tesseract OCR).  
2. **Base de Datos**:  
   - **Vectorial**: Weaviate/FAISS para embeddings de pólizas.  
   - **Metadatos**: PostgreSQL (usuarios, versiones, permisos).  

### 4.2 Backend e IA  
- **LangChain**:  
  - **RAG**: Combina base de datos + búsqueda web (Serper API).  
  - **Agentes**: `document_retriever`, `web_searcher`, `fallback_agent`.  
- **Modelo Generativo**: GPT-4 o Llama 3 (auto-hospedado).  
- **API**: FastAPI con endpoints:  
  ```python
  @app.post("/chat")
  async def chat(query: str): 
      # Lógica de agentes aquí

## 4.3 Frontend (Next.js)  
- **Editor**:  
  - Uso de `TipTap` o `CKEditor` para edición enriquecida de texto.  
- **Renderizado de PDF**:  
  - Integración de `PDF.js` de Mozilla para previsualización de documentos en el navegador.  
- **WebSockets**:  
  - Sincronización en tiempo real usando `Ably` o bibliotecas como `Socket.IO`.  

## 4.4 DevOps  
- **Docker**:  
  - Contenedores independientes para:  
    - Backend (FastAPI).  
    - Frontend (Next.js).  
    - Bases de datos (PostgreSQL + Weaviate/FAISS).  
- **CI/CD**:  
  - Flujos automatizados con GitHub Actions para testing y despliegue en entornos staging/producción.  

---

## 5. Entregables  
1. **Análisis Exploratorio (EDA)**:  
   - Jupyter Notebook con visualizaciones de distribución de términos, cláusulas comunes y métricas de documentos.  
2. **Scripts de Preprocesamiento**:  
   - `pdf_to_text.py`: Extracción de texto desde PDFs (PyPDF2 + OCR opcional).  
   - `docx_to_text.py`: Procesamiento de archivos Word.  
   - `clean_policy_data.py`: Normalización de texto (eliminación de headers, stopwords).  
3. **Base de Datos**:  
   - Archivo `docker-compose.yml` para despliegue de:  
     - PostgreSQL (metadatos).  
     - Weaviate/FAISS (vectores de políticas).  
4. **Sistema LangChain**:  
   - Agentes en `agents.py` con lógica para:  
     - Enrutar preguntas a base de datos, web (Serper API) o respuestas estándar.  
5. **API**:  
   - FastAPI con endpoints documentados en Swagger (`/docs`).  
6. **UI**:  
   - Aplicación Next.js con:  
     - Editor de políticas en panel izquierdo.  
     - Chat en panel derecho (estilo ChatGPT).  
     - Dashboard de analytics.  
7. **Demo**:  
   - Video demostrativo (5-10 min).  
   - Docker-compose para despliegue local.  

---

## 6. Cronograma  

| Fase               | Duración      |  
|---------------------|--------------|  
| EDA y Preparación   | 1 semana     |  
| Backend + IA        | 2 semanas    |  
| Frontend            | 1 semanas    |  
| Pruebas y Demo      | 1 semana     |  

---

## 7. Métricas de Éxito  

- **Compromiso**:  
  - Sesiones promedio > 10 minutos.  
  - Tasa de retención > 70% en primera semana.  
- **Precisión**:  
  - >90% respuestas correctas en QA interno (basado en 100 preguntas de prueba).  
- **Adopción**:  
  - 3+ aseguradoras en fase piloto (contratos firmados).  

---

## 8. Riesgos y Mitigación  

| **Riesgo**                          | **Mitigación**                                      |  
|-------------------------------------|----------------------------------------------------|  
| Alucinaciones en cláusulas generadas por IA. | Uso de `Llama Guard` para filtrado constitucional. |  
| Latencia en búsqueda de documentos. | Optimización de índices FAISS/Weaviate + caching.  |  
| Integración compleja de WebSockets. | Uso de servicios gestionados como Ably o Pusher.   |  