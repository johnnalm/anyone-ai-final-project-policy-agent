# Policy AI - Product Requirements Document (PRD)

## 1. Product Overview  
Policy AI is a **SaaS platform** that combines generative AI, document analysis, and real-time collaboration to simplify the creation, analysis, and modification of insurance policies.  

**Version**: 1.0  
**Target audience**: Insurance companies, business owners, risk managers.  

---

## 2. Main Objectives  
1. Create customized policies through conversational AI.  
2. Analyze and summarize existing policies (PDF/Word).  
3. Modify terms with visual change tracking interface.  
4. Integrate web search for responses with up-to-date information.  
5. Offer an **Intuitive and User-Friendly UI/UX** for businesses.  

---

## 3. Key Features  

### 3.1 Conversational AI Chat  
- **Three interaction modes**:  
  - **Policy creation**: Guides the user through questions to generate policies.  
  - **Policy analysis**: Summary of clauses, explanation of technical terms.  
  - **Policy modification**: Suggests edits (e.g.: "Increase coverage to $2M").  
- **LangChain Agents**:  
  - Dynamically decides whether to search the database, the web, or delegate to a human.  

### 3.2 Document Editor with Track Changes  
- **Visual interface**:  
  - **Left panel**: Policy editor (Word Online style).  
  - **Right panel**: AI chat.  
  - **Highlighted changes**:  
    - Green: Additions (with real-time cursor effect).  
    - Red: Deletions (strikethrough).  
  - **Version history**: Revision comparison.  
- **Export**: PDF/Word with change tracking.  

### 3.3 Premium UI/UX Requirements  
- **Dashboard**: Minimalist design with dark/light mode.  
- **Real-time collaboration**: Multiple users with permissions (Admin/Editor/Reader).  
- **White-label**: Customization for insurance companies.  
- **Accessibility**: WCAG 2.1 compliance.  

---

## 4. Technical Architecture  

### 4.1 Data Flow  
1. **EDA and Preprocessing**:  
   - Python scripts (`PyPDF2`, `python-docx`, Tesseract OCR).  
2. **Database**:  
   - **Vector**: Weaviate/FAISS for policy embeddings.  
   - **Metadata**: PostgreSQL (users, versions, permissions).  

### 4.2 Backend and AI  
- **LangChain**:  
  - **RAG**: Combines database + web search (Serper API).  
  - **Agents**: `document_retriever`, `web_searcher`, `fallback_agent`.  
- **Generative Model**: GPT-4 or Llama 3 (self-hosted).  
- **API**: FastAPI with endpoints:  
  ```python
  @app.post("/chat")
  async def chat(query: str): 
      # Agent logic here
  ```

### 4.3 Frontend (Next.js)  
- **Editor**:  
  - Uses `TipTap` or `CKEditor` for rich text editing.  
- **PDF Rendering**:  
  - Integration of Mozilla's `PDF.js` for document preview in the browser.  
- **WebSockets**:  
  - Real-time synchronization using `Ably` or libraries like `Socket.IO`.  

### 4.4 DevOps  
- **Docker**:  
  - Independent containers for:  
    - Backend (FastAPI).  
    - Frontend (Next.js).  
    - Databases (PostgreSQL + Weaviate/FAISS).  
- **CI/CD**:  
  - Automated workflows with GitHub Actions for testing and deployment in staging/production environments.  

---

## 5. Deliverables  
1. **Exploratory Data Analysis (EDA)**:  
   - Jupyter Notebook with visualizations of term distribution, common clauses, and document metrics.  
2. **Preprocessing Scripts**:  
   - `pdf_to_text.py`: Text extraction from PDFs (PyPDF2 + optional OCR).  
   - `docx_to_text.py`: Word file processing.  
   - `clean_policy_data.py`: Text normalization (removing headers, stopwords).  
3. **Database**:  
   - `docker-compose.yml` file for deploying:  
     - PostgreSQL (metadata).  
     - Weaviate/FAISS (policy vectors).  
4. **LangChain System**:  
   - Agents in `agents.py` with logic for:  
     - Routing questions to database, web (Serper API), or standard responses.  
5. **API**:  
   - FastAPI with endpoints documented in Swagger (`/docs`).  
6. **UI**:  
   - Next.js application with:  
     - Policy editor in left panel.  
     - Chat in right panel (ChatGPT style).  
     - Analytics dashboard.  
7. **Demo**:  
   - Demonstration video (5-10 min).  
   - Docker-compose for local deployment.  

---

## 6. Timeline  

| Phase               | Duration     |  
|---------------------|--------------|  
| EDA and Preparation | 1 week       |  
| Backend + AI        | 2 weeks      |  
| Frontend            | 1 week       |  
| Testing and Demo    | 1 week       |  

---

## 7. Success Metrics  

- **Engagement**:  
  - Average sessions > 10 minutes.  
  - Retention rate > 70% in first week.  
- **Accuracy**:  
  - >90% correct answers in internal QA (based on 100 test questions).  
- **Adoption**:  
  - 3+ insurance companies in pilot phase (signed contracts).  

---

## 8. Risks and Mitigation  

| **Risk**                           | **Mitigation**                                     |  
|------------------------------------|----------------------------------------------------|  
| Hallucinations in AI-generated clauses. | Use of `Llama Guard` for constitutional filtering. |  
| Latency in document search.        | Optimization of FAISS/Weaviate indices + caching.  |  
| Complex WebSocket integration.     | Use of managed services like Ably or Pusher.       | 