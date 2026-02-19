 AI Insurance Advisor: RAG-Powered Policy GuideAn intelligent conversational assistant that helps travelers navigate complex insurance policies using Retrieval-Augmented Generation (RAG). This system transforms dense legal documents into clear, personalized coverage explanations.🚀 Key FeaturesSmart Policy Search: Natural language querying of insurance PDFs.Personalized Recommendations: LangChain agents that compare coverage against specific user trip details.Secure Authentication: JWT-based login/signup for personalized data isolation.Real-time Streaming: AI responses streamed from FastAPI to React for a smooth chat experience.🏗️ Architecture & Tech StackFrontend (ReactJS)Framework: React 18+ with Functional Components & Hooks.Styling: Tailwind CSS for a modern, responsive UI.API Client: Axios with Interceptors for JWT handling.Backend (Python/FastAPI)Orchestration: LangChain (Agents & Chains).LLM: OpenAI GPT-4 (or Llama 3/Gemini via LangChain).Database: SQLAlchemy (User Data) + Pinecone/ChromaDB (Vector Database).Authentication: Python-Jose (JWT) & Passlib (Bcrypt).📁 Project StructurePlaintext.
├── client/                 # React Frontend
│   ├── public/
│   └── src/
│       ├── components/     # Reusable UI (Auth, Chat, Navbar)
│       └── pages/          # Login, Signup, Dashboard
├── server/                 # Python Backend
│   ├── app/
│   │   ├── api/            # FastAPI Routes (Auth, Chat)
│   │   ├── core/           # Security & RAG Logic
│   │   └── db/             # Database Models & Sessions
│   └── data/               # Source Insurance PDFs
├── .env.example            # Environment variables template
└── requirements.txt        # Python dependencies
🛠️ Installation & Setup1. PrerequisitesPython 3.9+Node.js 16+API Keys: OpenAI (or Gemini), Pinecone (optional for local vector storage).2. Backend SetupBashcd server
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt

# Create .env file and add your keys
touch .env 
3. Frontend SetupBashcd client
npm install
npm start
🤖 RAG Implementation DetailsThe core of this advisor follows a 4-step pipeline:Ingestion: Insurance PDFs are split using RecursiveCharacterTextSplitter.Embedding: Text chunks are converted to vectors using text-embedding-3-small.Retrieval: Relevant clauses are pulled from the Vector Store based on user queries.Augmentation: A custom Prompt Template instructs the LLM to only answer based on the retrieved policy context to prevent "hallucinations."📄 Environment VariablesTo run this project, you will need to add the following variables to your .env file in the /server directory:VariableDescriptionDATABASE_URLYour SQLite or PostgreSQL connection stringSECRET_KEYSecret for JWT signingOPENAI_API_KEYYour AI model API keyPINECONE_API_KEYIf using Pinecone for vector storage🤝 ContributingContributions are what make the open-source community such an amazing place to learn, inspire, and create.Fork the Project.Create your Feature Branch (git checkout -b feature/AmazingFeature).Commit your Changes (git commit -m 'Add some AmazingFeature').Push to the Branch (git push origin feature/AmazingFeature).Open a Pull Request.
