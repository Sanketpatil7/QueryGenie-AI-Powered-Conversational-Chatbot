<div align="center">

# 🤖 QueryGenie
### AI-Powered Conversational Chatbot with E-Commerce Intelligence

![Spring Boot](https://img.shields.io/badge/Spring%20Boot-2.7.17-brightgreen?style=for-the-badge&logo=springboot)
![Java](https://img.shields.io/badge/Java-8-orange?style=for-the-badge&logo=java)
![MySQL](https://img.shields.io/badge/MySQL-8.0-blue?style=for-the-badge&logo=mysql)
![Docker](https://img.shields.io/badge/Docker-Containerized-2496ED?style=for-the-badge&logo=docker)
![Gemini](https://img.shields.io/badge/Gemini-2.0%20Flash-purple?style=for-the-badge&logo=google)
![WebSocket](https://img.shields.io/badge/WebSocket-Real--Time-red?style=for-the-badge)

<p align="center">
  A real-time AI chatbot that intelligently routes queries between <b>Google Gemini 2.0 Flash LLM</b> and a <b>MySQL product database</b> using intent-based routing over WebSocket.
</p>

</div>

---

## 📌 Overview

QueryGenie is a full-stack AI-powered chatbot that operates in two intelligent modes:

- **AI Mode** — General questions are routed to the **Google Gemini 2.0 Flash API** for smart, conversational responses
- **E-Commerce Mode** — Product queries like *"Show ACs under ₹30,000"* are detected via **Regex intent routing** and resolved from a **MySQL product database**
- **Sentiment Mode** — Sentiment analysis responses are visualized as an interactive **Chart.js pie chart**

Communication between the frontend and backend happens over a **persistent WebSocket connection** for real-time, low-latency messaging.

---

## 🏗️ Architecture

```
Browser (HTML/JS/CSS)
       ↕  WebSocket (ws://localhost:8080/chat)
Spring Boot Backend
       ↓               ↓
 Gemini 2.0 API     MySQL Database
 (AI responses)   (Product catalog)
```

---

## ✨ Features

| Feature | Description |
|---|---|
| 🤖 AI Responses | Powered by Google Gemini 2.0 Flash via REST API |
| 🛒 Product Search | Natural language queries resolved from MySQL |
| 🔀 Intent Routing | Keyword + Regex pattern matching for dual-mode routing |
| 📊 Sentiment Chart | Dynamic Chart.js pie chart for sentiment analysis results |
| 🔌 Real-Time Chat | Persistent WebSocket connection (no HTTP polling) |
| 💬 Chat History | Sessions saved in localStorage |
| 🐳 Dockerized | Multi-stage Dockerfile for production-ready deployment |

---

## 🛠️ Tech Stack

### Backend
- **Java 8** + **Spring Boot 2.7.17**
- **Spring WebSocket** — `TextWebSocketHandler` for real-time messaging
- **Spring Data JPA** + **Hibernate** — ORM with derived query methods
- **RestTemplate** — HTTP client for Gemini API integration
- **MySQL 8.0** — Relational database for product catalog
- **Maven** — Dependency management and build tool
- **Docker** — Multi-stage containerization

### Frontend
- **HTML5 / CSS3 / Vanilla JavaScript**
- **WebSocket API** — Native browser WebSocket client
- **Chart.js** — Sentiment analysis pie chart visualization

### External API
- **Google Gemini 2.0 Flash** — Large Language Model for AI responses

---

## 📁 Project Structure

```
QueryGenie/
├── Dockerfile                          # Multi-stage Docker build
├── pom.xml                             # Maven dependencies
└── src/main/
    ├── java/com/genie/ai/
    │   ├── config/
    │   │   └── WebSocketConfig.java    # Registers /chat WebSocket endpoint
    │   ├── cache/
    │   │   └── CacheConfig.java        # Enables Spring caching
    │   ├── controller/
    │   │   ├── GeminiWebSocketHandler.java  # Core routing & AI logic
    │   │   └── ChatRequest.java
    │   ├── entity/
    │   │   └── Product.java            # JPA entity → products table
    │   └── repo/
    │       └── ProductRepository.java  # Derived query: findByCategoryAndPriceLessThan
    └── resources/
        └── application.properties      # DB config, API key, server settings
```

---

## ⚙️ How It Works

### Intent Routing Logic
```
User Message
     ↓
Contains product keyword (ac/tv/fridge) + price word (under/below)?
     ↓ YES                          ↓ NO
E-Commerce Mode               AI Mode (Gemini)
     ↓                              ↓
Regex extracts price          RestTemplate POST
     ↓                         to Gemini API
JPA query MySQL                    ↓
     ↓                        Parse candidates[0]
Format product list           Return AI response
     ↓                              ↓
         Send via WebSocket session
```

---

## 🚀 Getting Started

### Prerequisites
- Java 8+
- Maven 3.6+
- MySQL 8.0
- Docker (optional)

### 1. Clone the Repository
```bash
git clone https://github.com/yourusername/QueryGenie.git
cd QueryGenie
```

### 2. Set Up MySQL Database
```sql
CREATE DATABASE ecomm;
USE ecomm;

CREATE TABLE product (
  id BIGINT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(255),
  category VARCHAR(100),
  price DOUBLE,
  description TEXT
);

-- Insert sample data
INSERT INTO product VALUES (null, 'Samsung 1.5 Ton AC', 'Ac', 28999, 'Energy efficient split AC');
INSERT INTO product VALUES (null, 'LG Double Door Fridge', 'Refrigerator', 32000, '260L frost-free refrigerator');
INSERT INTO product VALUES (null, 'Sony 43 inch LED TV', 'Television', 35000, '4K Ultra HD Smart TV');
```

### 3. Configure application.properties
```properties
gemini.api.key=YOUR_GEMINI_API_KEY_HERE
spring.datasource.url=jdbc:mysql://localhost:3306/ecomm
spring.datasource.username=your_username
spring.datasource.password=your_password
```

> 🔑 Get your free Gemini API key at [aistudio.google.com](https://aistudio.google.com)

### 4. Run the Backend
```bash
mvn spring-boot:run
```

### 5. Open the Frontend
Open `UI/index.html` in your browser — that's it!

---

## 🐳 Docker Deployment

```bash
# Build the Docker image
docker build -t querygenie .

# Run the container
docker run -p 8080:8080 querygenie
```

The Dockerfile uses a **multi-stage build**:
- **Stage 1** — Maven + JDK 8 compiles the source and packages the JAR
- **Stage 2** — Lightweight OpenJDK slim image runs the JAR

This reduces the final image size significantly by excluding build tools from the runtime image.

---

## 💬 Sample Queries

| You Type | What Happens |
|---|---|
| `Show ACs under 30000` | Queries MySQL → returns matching AC products |
| `Refrigerators below 40000` | Queries MySQL → returns matching fridges |
| `What is machine learning?` | Calls Gemini API → returns AI explanation |
| `Explain WebSocket` | Calls Gemini API → returns AI response |
| `Analyze sentiment of this review...` | Gemini returns JSON → Chart.js renders pie chart |

---

## 🔮 Future Improvements

- [ ] Spring Security + JWT authentication
- [ ] Move API key to environment variables / secrets manager
- [ ] `@Cacheable` on product queries to reduce DB load
- [ ] Stream Gemini responses token-by-token for better UX
- [ ] Deploy to AWS EC2 / Railway / Render
- [ ] Add more product categories and filters

---

## 👨‍💻 Author

**Sanket** — Full Stack Developer  
📧 issanketpatil1712@gmail.com 
🔗 [LinkedIn](https://linkedin.com/in/yourprofile) | [GitHub](https://github.com/yourusername)

---

<div align="center">
  <i>Built with ☕ Java, 🤖 Gemini AI, and 💡 curiosity</i>
</div>
