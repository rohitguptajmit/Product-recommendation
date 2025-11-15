🧠 Semantic Product Recommendation Using Db2 Vector AI + OpenAI Embeddings

A complete end-to-end AI-powered semantic search system using:

Db2 12.1.3 Vector AI

OpenAI text-embedding-3-large

Python + SQLAlchemy + ipython-sql

Synthetic dataset generation

T-SNE visualization

Image-based shoe similarity search

This repository demonstrates how to build vector search, embedding pipelines, and AI-driven product recommendations using structured + unstructured data.

🖼️ Architecture Diagram
flowchart LR
    A[🧑‍💻 Jupyter Notebook<br>Python + OpenAI] --> B[🔢 Generate Embeddings<br>OpenAI text-embedding-3-large]
    B --> C[(🗄️ Db2 12.1.3)]
    
    subgraph Db2 Vector AI
        C --> C1[📥 Load Product Data<br>CSV / SQL Import]
        C1 --> C2[🧬 EMBEDDING VECTOR(1024)<br>Stored in Db2]
        C2 --> C3[🔍 VECTOR_DISTANCE<br>Similarity Search]
    end
    
    C3 --> D[🔁 Ranked Similar Shoes]
    D --> E[🖼️ Visual Display<br>(shoe images, T-SNE)]

📦 Repository Structure
├── shoes-data-generation.ipynb     # Create dataset
├── shoes-data-partitioning.ipynb   # Store-wise partitioning
├── shoes-search.ipynb              # Semantic search + visualization
├── shoes.csv                       # Base product data
├── shoes-vectors.csv               # Products + embeddings (prebuilt)
├── images/                         # Shoe photos
├── utils.py                        # Shared helper functions
├── requirements.txt                # Python dependencies
├── .env-sample                     # Template for env variables
└── README.md                       # This file

🚀 Features
✔ OpenAI-based Embedding Generation

Uses text-embedding-3-large to convert product attributes into high-dimensional semantic vectors.

✔ Db2 VECTOR Column

Stores embeddings in native optimized format:

EMBEDDING VECTOR(1024, FLOAT32)

✔ Semantic Search with VECTOR_DISTANCE

Retrieve similar products using:

EUCLIDEAN

DOT_PRODUCT

COSINE (via normalization)

✔ Image-based Recommendation

Displays similarity results with product photos.

✔ T-SNE Visualization

Plots product embeddings in 2-D space.

✔ Fully Parameterized SQL + .env Support

Uses secure environment variables for:

Db2 credentials

OpenAI API keys

⚙️ Setup Instructions
1️⃣ Create Virtual Environment
uv venv --python=python3.12
source .venv/bin/activate

2️⃣ Install Dependencies
uv pip install -r requirements.txt

🔐 Configure .env

Rename .env-sample → .env and update:

# OpenAI
OPENAI_API_KEY=your_key
OPENAI_BASE_URL="https://api.openai.com/v1"
OPENAI_EMBED_MODEL="text-embedding-3-large"

# Db2 credentials
database=test1
hostname=localhost
port=50000
protocol=TCPIP
uid=db2inst1
pwd=password

🗄️ Db2 Table Structure
Product Data
CREATE TABLE SQ_SHOES (
  SKU VARCHAR(8),
  PRODUCT_NAME VARCHAR(23),
  BRAND VARCHAR(9),
  CLASS VARCHAR(5),
  TYPE VARCHAR(7),
  MATERIAL VARCHAR(9),
  COLOR VARCHAR(5),
  WEATHER_RESISTANCE VARCHAR(10),
  ARCH_SUPPORT VARCHAR(4),
  SIZE FLOAT,
  PRICE FLOAT,
  RATING FLOAT,
  STORE_ID BIGINT,
  CITY VARCHAR(7)
);

Add Embedding Column
ALTER TABLE SQ_SHOES
ADD COLUMN EMBEDDING VECTOR(1024, FLOAT32);

🤖 Generating Embeddings with OpenAI
from openai import OpenAI
from dotenv import load_dotenv
import os

load_dotenv()

client = OpenAI(
    api_key=os.getenv("OPENAI_API_KEY"),
    base_url=os.getenv("OPENAI_BASE_URL")
)

embed_model = os.getenv("OPENAI_EMBED_MODEL")

response = client.embeddings.create(
    model=embed_model,
    input=shoe_text
)

embedding = response.data[0].embedding

🔌 Connecting to Db2 from Jupyter
db2creds = dotenv_values('.env')
%sql CONNECT CREDENTIALS db2creds

📥 Loading Data into Db2
CSV → Db2 via IMPORT
IMPORT FROM 'shoes-vectors.csv' OF DEL
  MODIFIED BY skipcount 1
  INSERT INTO SQ_SHOES

🧠 Semantic Similarity Search
SELECT  
  s2.SKU,
  s2.PRODUCT_NAME,
  VECTOR_DISTANCE(s1.EMBEDDING, s2.EMBEDDING, EUCLIDEAN) AS dist
FROM SQ_SHOES s1
JOIN SQ_SHOES s2 ON s1.SKU <> s2.SKU
WHERE s1.SKU = :my_sku
ORDER BY dist
FETCH FIRST 3 ROWS ONLY;

🎨 Visualization Example
T-SNE Plot
plot_similarity_tsne(df_vectors, my_choice_sku)

Display Images
display_sku_images(["SKU001", "SKU145", "SKU322"])

🖼️ Workflow Diagram
sequenceDiagram
    participant NB as Jupyter Notebook
    participant OA as OpenAI API
    participant DB as Db2 12.1.3
    participant IMG as Image Renderer

    NB->>OA: Generate text embeddings\n(text-embedding-3-large)
    OA-->>NB: Return vector (1024-dim)
    NB->>DB: INSERT vector into VECTOR column
    NB->>DB: Run semantic search\nVECTOR_DISTANCE()
    DB-->>NB: Similar SKUs ranked by similarity
    NB->>IMG: Display images + charts

🎯 Final Output Example

✔ Input shoe
✔ Top-3 most similar shoes
✔ Distance scores
✔ Images
✔ T-SNE cluster plot

This provides a complete end-to-end semantic search experience.

🤝 Contribute

PRs and suggestions are welcome.
This repo is an excellent foundation for:

Retail AI use cases

Vector databases

Recommendation engines

AI search demonstrations

Db2 + OpenAI integration patterns

⭐ If you found this useful…

Leave a star ⭐ on the repository — it helps others discover this solution!