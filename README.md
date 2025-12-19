# Amendment System

An internal amendment tracking system for managing application updates.

## Features

- Track past, current, and future amendments
- Unique ID for each amendment
- RESTful API backend (FastAPI + SQLite)
- Modern React frontend
- Full CRUD operations
- Timeline view of amendments

## Tech Stack

- **Backend**: Python FastAPI
- **Frontend**: React
- **Database**: SQLite
- **API Documentation**: Auto-generated with FastAPI/Swagger

## Project Structure

```
amendment-system/
├── backend/           # FastAPI backend
│   ├── app/
│   │   ├── models.py      # Database models
│   │   ├── schemas.py     # Pydantic schemas
│   │   ├── crud.py        # CRUD operations
│   │   ├── database.py    # Database connection
│   │   └── main.py        # FastAPI app
│   └── requirements.txt
├── frontend/          # React frontend
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   └── App.js
│   └── package.json
├── tests/            # Test suite
└── docs/             # Documentation
```

## Getting Started

### Backend Setup

```bash
cd backend
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
pip install -r requirements.txt
uvicorn app.main:app --reload
```

### Frontend Setup

```bash
cd frontend
npm install
npm start
```

## API Endpoints

- `GET /amendments` - List all amendments
- `GET /amendments/{id}` - Get specific amendment
- `POST /amendments` - Create new amendment
- `PUT /amendments/{id}` - Update amendment
- `DELETE /amendments/{id}` - Delete amendment

## Development

This project uses Claude Orchestra for autonomous development. See TODO.md for planned features and tasks.
