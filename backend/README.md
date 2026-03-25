# Task Manager App

## Tech Stack
- Backend: Django + Django REST Framework (ViewSet)
- Frontend: Vue 3 (Composition API)
- Database: SQLite

## Features
- Create, Read, Update, Delete tasks
- Toggle task completion
- Input validation
- Basic loading and error handling

## Setup Instructions

### Backend
cd backend
python -m venv venv
source venv/bin/activate
pip install django djangorestframework django-cors-headers
python manage.py migrate
python manage.py runserver

### Frontend
cd frontend
npm install
npm run dev

## API Endpoints

GET /tasks/  
POST /tasks/  
GET /tasks/{id}/  
PUT /tasks/{id}/  
PATCH /tasks/{id}/  
DELETE /tasks/{id}/  

## Notes
- Used ViewSet instead of ModelViewSet as required
- Focused on clean API structure and working integration