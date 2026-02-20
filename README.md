Project Overview

Project Name Suggestion:

insurance-rag-advisor


This project will later include:

🔐 Authentication (Login / Signup)

🤖 RAG-based Insurance Advisor (LangChain)

🧠 Policy embeddings (Vector DB)

💬 Conversational Interface

For now, we build Authentication System (Phase 1).

🏗️ Final Project Structure
insurance-rag-advisor/
│
├── backend/
│   ├── app.py
│   ├── models.py
│   ├── config.py
│   ├── requirements.txt
│   └── .env
│
├── frontend/
│   ├── package.json
│   └── src/
│       ├── App.js
│       ├── pages/
│       │   ├── Login.js
│       │   └── Signup.js
│       └── services/
│           └── api.js
│
└── README.md

🔥 STEP 1 — Backend (Python + Flask + JWT + SQLite)
📌 1. Create Backend Folder
mkdir insurance-rag-advisor
cd insurance-rag-advisor
mkdir backend
cd backend

📌 2. Create Virtual Environment
python -m venv venv


Activate:

Windows

venv\Scripts\activate


Mac/Linux

source venv/bin/activate

📌 3. Install Dependencies
pip install flask flask-cors flask-jwt-extended flask-sqlalchemy python-dotenv werkzeug


Create requirements.txt:

pip freeze > requirements.txt

📌 4. Create .env
JWT_SECRET_KEY=super-secret-key

📌 5. Create config.py
import os
from dotenv import load_dotenv

load_dotenv()

class Config:
    SECRET_KEY = os.getenv("JWT_SECRET_KEY")
    SQLALCHEMY_DATABASE_URI = "sqlite:///users.db"
    SQLALCHEMY_TRACK_MODIFICATIONS = False

📌 6. Create models.py
from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    email = db.Column(db.String(120), unique=True, nullable=False)
    password = db.Column(db.String(200), nullable=False)

📌 7. Create app.py
from flask import Flask, request, jsonify
from flask_cors import CORS
from flask_jwt_extended import (
    JWTManager,
    create_access_token,
    jwt_required,
    get_jwt_identity,
)
from werkzeug.security import generate_password_hash, check_password_hash
from config import Config
from models import db, User

app = Flask(__name__)
app.config.from_object(Config)

CORS(app)
db.init_app(app)
jwt = JWTManager(app)

with app.app_context():
    db.create_all()

# -------------------
# Signup
# -------------------
@app.route("/signup", methods=["POST"])
def signup():
    data = request.json
    email = data.get("email")
    password = data.get("password")

    if User.query.filter_by(email=email).first():
        return jsonify({"msg": "User already exists"}), 400

    hashed_password = generate_password_hash(password)

    new_user = User(email=email, password=hashed_password)
    db.session.add(new_user)
    db.session.commit()

    return jsonify({"msg": "User created successfully"}), 201


# -------------------
# Login
# -------------------
@app.route("/login", methods=["POST"])
def login():
    data = request.json
    email = data.get("email")
    password = data.get("password")

    user = User.query.filter_by(email=email).first()

    if not user or not check_password_hash(user.password, password):
        return jsonify({"msg": "Invalid credentials"}), 401

    access_token = create_access_token(identity=email)

    return jsonify(access_token=access_token)


# -------------------
# Protected Route
# -------------------
@app.route("/profile", methods=["GET"])
@jwt_required()
def profile():
    current_user = get_jwt_identity()
    return jsonify(logged_in_as=current_user)


if __name__ == "__main__":
    app.run(debug=True)

▶️ Run Backend
python app.py


Backend runs at:

http://localhost:5000

🚀 STEP 2 — Frontend (React)
📌 1. Create React App

Go to root folder:

cd ..
npx create-react-app frontend
cd frontend
npm install axios react-router-dom

📌 2. Create API Service

📁 src/services/api.js

import axios from "axios";

const API = axios.create({
  baseURL: "http://localhost:5000",
});

export default API;

📌 3. Create Signup Page

📁 src/pages/Signup.js

import React, { useState } from "react";
import API from "../services/api";
import { useNavigate } from "react-router-dom";

function Signup() {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const navigate = useNavigate();

  const handleSignup = async (e) => {
    e.preventDefault();
    try {
      await API.post("/signup", { email, password });
      alert("Signup successful!");
      navigate("/login");
    } catch (err) {
      alert("User already exists");
    }
  };

  return (
    <div>
      <h2>Signup</h2>
      <form onSubmit={handleSignup}>
        <input
          type="email"
          placeholder="Email"
          onChange={(e) => setEmail(e.target.value)}
          required
        />
        <br /><br />
        <input
          type="password"
          placeholder="Password"
          onChange={(e) => setPassword(e.target.value)}
          required
        />
        <br /><br />
        <button type="submit">Signup</button>
      </form>
    </div>
  );
}

export default Signup;

📌 4. Create Login Page

📁 src/pages/Login.js

import React, { useState } from "react";
import API from "../services/api";

function Login() {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");

  const handleLogin = async (e) => {
    e.preventDefault();
    try {
      const res = await API.post("/login", { email, password });
      localStorage.setItem("token", res.data.access_token);
      alert("Login successful!");
    } catch (err) {
      alert("Invalid credentials");
    }
  };

  return (
    <div>
      <h2>Login</h2>
      <form onSubmit={handleLogin}>
        <input
          type="email"
          placeholder="Email"
          onChange={(e) => setEmail(e.target.value)}
          required
        />
        <br /><br />
        <input
          type="password"
          placeholder="Password"
          onChange={(e) => setPassword(e.target.value)}
          required
        />
        <br /><br />
        <button type="submit">Login</button>
      </form>
    </div>
  );
}

export default Login;

📌 5. Update App.js
import React from "react";
import { BrowserRouter, Routes, Route } from "react-router-dom";
import Login from "./pages/Login";
import Signup from "./pages/Signup";

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Login />} />
        <Route path="/login" element={<Login />} />
        <Route path="/signup" element={<Signup />} />
      </Routes>
    </BrowserRouter>
  );
}

export default App;

📦 GitHub README.md (Copy This)

Below is your full GitHub-ready README content 👇

# 🛡️ Insurance RAG Advisor

An AI-powered Insurance Advisor that provides personalized policy explanations using RAG (Retrieval-Augmented Generation) and LangChain Agents.

## 🚀 Phase 1: Authentication System

This phase includes:

- User Signup
- User Login
- JWT Authentication
- Protected API
- React Frontend
- Flask Backend

---

## 🏗 Tech Stack

Frontend:
- ReactJS
- Axios
- React Router

Backend:
- Python
- Flask
- Flask-JWT-Extended
- SQLAlchemy
- SQLite

---

## 📂 Project Structure

insurance-rag-advisor/
│
├── backend/
├── frontend/
└── README.md

---

## ⚙️ Backend Setup

cd backend  
python -m venv venv  
source venv/bin/activate  (Mac/Linux)  
venv\Scripts\activate  (Windows)  

pip install -r requirements.txt  
python app.py  

Backend runs at:
http://localhost:5000

---

## ⚛️ Frontend Setup

cd frontend  
npm install  
npm start  

Frontend runs at:
http://localhost:3000

---

## 🔐 API Endpoints

POST /signup  
POST /login  
GET /profile (Protected)

---

## 🧠 Upcoming Phases

- RAG Pipeline with Insurance Policies
- Vector Database (FAISS / Pinecone)
- LangChain Agent Tools
- Policy Comparison Engine
- Conversational UI

---

## 📌 Future Architecture

React UI → Flask API → LangChain Agent → Vector DB → Insurance Knowledge Base

---

## 📜 License

For educational and research use.# Travel-Insurance-AI
