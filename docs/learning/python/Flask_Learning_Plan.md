# Flask Learning Plan - Practical Guide

**Created:** 2026-03-22
**Target Audience:** Personal learning path
**Time Required:** ~40-50 hours (8-10 weeks, 5 hours per week)

---

## Why Flask?

**Advantages:**
- Micro-framework - simple, easy to learn
- Minimal boilerplate - quick to get started
- Flexible - use only what you need
- Excellent for building REST APIs
- Perfect for microservices

**When to use:**
- Small to medium REST APIs
- Microservices (like platform MCP servers)
- Prototypes, MVPs
- Internal tooling (CLI wrappers, dashboards)

---

## Module 1: Flask Basics (5 hours)

### 1.1 - Hello World & Routing (1 hour)

**Installation:**
```bash
mkdir -p ~/learning/flask-tutorial
cd ~/learning/flask-tutorial
python -m venv venv
source venv/bin/activate
pip install flask
```

**First application:**
```python
# app.py
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
return 'Hello, Flask!'

@app.route('/user/<username>')
def show_user(username):
return f'Hello, {username}!'

if __name__ == '__main__':
app.run(debug=True)
```

**Exercise 1.1:**
```python
# Create routes:
# GET /api/health - returns: {"status": "healthy"}
# GET /api/version - returns: {"version": "1.0.0"}
# GET /api/user/<id> - returns: {"user_id": id, "name": "..."}

from flask import Flask, jsonify

app = Flask(__name__)

@app.route('/api/health')
def health():
return jsonify({"status": "healthy"})

@app.route('/api/version')
def version():
return jsonify({"version": "1.0.0"})

@app.route('/api/user/<int:user_id>')
def get_user(user_id):
# Mock data
users = {
1: {"user_id": 1, "name": "demo_user", "role": "SRE"},
2: {"user_id": 2, "name": "Alice", "role": "Developer"}
}
return jsonify(users.get(user_id, {"error": "User not found"}))

if __name__ == '__main__':
app.run(debug=True)
```

**Testing:**
```bash
# In terminal:
curl http://localhost:5000/api/health
curl http://localhost:5000/api/user/1
```

---

### 1.2 - HTTP Methods & Request Handling (2 hours)

**Request parsing:**
```python
from flask import Flask, request, jsonify

app = Flask(__name__)

# In-memory storage
tasks = []

@app.route('/tasks', methods=['GET'])
def get_tasks():
return jsonify(tasks)

@app.route('/tasks', methods=['POST'])
def create_task():
data = request.get_json()
task = {
'id': len(tasks) + 1,
'title': data.get('title'),
'completed': False
}
tasks.append(task)
return jsonify(task), 201

@app.route('/tasks/<int:task_id>', methods=['PUT'])
def update_task(task_id):
data = request.get_json()
for task in tasks:
if task['id'] == task_id:
task['completed'] = data.get('completed', task['completed'])
return jsonify(task)
return jsonify({'error': 'Task not found'}), 404

@app.route('/tasks/<int:task_id>', methods=['DELETE'])
def delete_task(task_id):
global tasks
tasks = [t for t in tasks if t['id'] != task_id]
return '', 204
```

**Exercise 1.2:**
```python
# TODO List API
# Build a simple REST API for task management:
# - POST /tasks - create new task
# - GET /tasks - retrieve all tasks
# - GET /tasks/<id> - retrieve one task
# - PUT /tasks/<id> - update task
# - DELETE /tasks/<id> - delete task
# - GET /tasks?completed=true - filter by status

# Solution:
from flask import Flask, request, jsonify

app = Flask(__name__)

tasks = []
task_id_counter = 1

@app.route('/tasks', methods=['GET'])
def get_tasks():
completed = request.args.get('completed')
if completed is not None:
completed_bool = completed.lower() == 'true'
filtered = [t for t in tasks if t['completed'] == completed_bool]
return jsonify(filtered)
return jsonify(tasks)

@app.route('/tasks/<int:task_id>', methods=['GET'])
def get_task(task_id):
task = next((t for t in tasks if t['id'] == task_id), None)
if task:
return jsonify(task)
return jsonify({'error': 'Task not found'}), 404

@app.route('/tasks', methods=['POST'])
def create_task():
global task_id_counter
data = request.get_json()

if not data or 'title' not in data:
return jsonify({'error': 'Title required'}), 400

task = {
'id': task_id_counter,
'title': data['title'],
'description': data.get('description', ''),
'completed': False
}
tasks.append(task)
task_id_counter += 1
return jsonify(task), 201

@app.route('/tasks/<int:task_id>', methods=['PUT'])
def update_task(task_id):
task = next((t for t in tasks if t['id'] == task_id), None)
if not task:
return jsonify({'error': 'Task not found'}), 404

data = request.get_json()
task['title'] = data.get('title', task['title'])
task['description'] = data.get('description', task['description'])
task['completed'] = data.get('completed', task['completed'])
return jsonify(task)

@app.route('/tasks/<int:task_id>', methods=['DELETE'])
def delete_task(task_id):
global tasks
tasks = [t for t in tasks if t['id'] != task_id]
return '', 204

if __name__ == '__main__':
app.run(debug=True)
```

**Testing:**
```bash
# Create
curl -X POST http://localhost:5000/tasks \
-H "Content-Type: application/json" \
-d '{"title": "Learn Flask", "description": "Complete tutorial"}'

# List
curl http://localhost:5000/tasks

# Update
curl -X PUT http://localhost:5000/tasks/1 \
-H "Content-Type: application/json" \
-d '{"completed": true}'

# Filter
curl http://localhost:5000/tasks?completed=true

# Delete
curl -X DELETE http://localhost:5000/tasks/1
```

---

### 1.3 - Templates & Static Files (2 hours)

**Jinja2 templates:**
```python
# app.py
from flask import Flask, render_template

app = Flask(__name__)

@app.route('/')
def index():
tasks = [
{'id': 1, 'title': 'Learn Flask', 'completed': False},
{'id': 2, 'title': 'Build API', 'completed': True}
]
return render_template('index.html', tasks=tasks)
```

```html
<!-- templates/index.html -->
<!DOCTYPE html>
<html>
<head>
<title>Task Manager</title>
<link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
</head>
<body>
<h1>My Tasks</h1>
<ul>
{% for task in tasks %}
<li class="{% if task.completed %}completed{% endif %}">
{{ task.title }}
</li>
{% endfor %}
</ul>
</body>
</html>
```

```css
/* static/style.css */
body {
font-family: Arial, sans-serif;
max-width: 600px;
margin: 50px auto;
}

.completed {
text-decoration: line-through;
color: gray;
}
```

**Exercise 1.3:**
```
Build a simple Task Manager web UI:
- List all tasks
- Form to add new tasks
- Checkbox to toggle completion
- Delete button
- CSS styling

File structure:
flask-task-app/
├── app.py
├── templates/
│  └── tasks.html
└── static/
└── style.css
```

---

## Module 2: Flask Extensions & Database (8 hours)

### 2.1 - Flask-SQLAlchemy (3 hours)

**Setup:**
```bash
pip install flask-sqlalchemy
```

**Database models:**
```python
# app.py
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///tasks.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

db = SQLAlchemy(app)

class Task(db.Model):
id = db.Column(db.Integer, primary_key=True)
title = db.Column(db.String(100), nullable=False)
description = db.Column(db.Text)
completed = db.Column(db.Boolean, default=False)
created_at = db.Column(db.DateTime, default=db.func.now())

def to_dict(self):
return {
'id': self.id,
'title': self.title,
'description': self.description,
'completed': self.completed,
'created_at': self.created_at.isoformat()
}

# Create tables
with app.app_context():
db.create_all()

@app.route('/tasks', methods=['GET'])
def get_tasks():
tasks = Task.query.all()
return jsonify([task.to_dict() for task in tasks])

@app.route('/tasks', methods=['POST'])
def create_task():
data = request.get_json()
task = Task(
title=data['title'],
description=data.get('description', '')
)
db.session.add(task)
db.session.commit()
return jsonify(task.to_dict()), 201
```

**Exercise 2.1:**
```python
# Project Management API
# Models:
# - Project (name, description, created_at)
# - Task (title, description, completed, project_id, assigned_to)
# - User (username, email, role)

# Implement:
# - One-to-many: Project -> Tasks
# - Many-to-one: Task -> User (assigned_to)
# - CRUD endpoints for all
# - Query: GET /projects/<id>/tasks
# - Query: GET /users/<id>/tasks

from flask import Flask, request, jsonify
from flask_sqlalchemy import SQLAlchemy
from datetime import datetime

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///project_manager.db'
db = SQLAlchemy(app)

# Models
class User(db.Model):
id = db.Column(db.Integer, primary_key=True)
username = db.Column(db.String(50), unique=True, nullable=False)
email = db.Column(db.String(100), unique=True, nullable=False)
role = db.Column(db.String(20), default='developer')
tasks = db.relationship('Task', backref='assigned_user', lazy=True)

def to_dict(self):
return {
'id': self.id,
'username': self.username,
'email': self.email,
'role': self.role
}

class Project(db.Model):
id = db.Column(db.Integer, primary_key=True)
name = db.Column(db.String(100), nullable=False)
description = db.Column(db.Text)
created_at = db.Column(db.DateTime, default=datetime.utcnow)
tasks = db.relationship('Task', backref='project', lazy=True, cascade='all, delete-orphan')

def to_dict(self, include_tasks=False):
result = {
'id': self.id,
'name': self.name,
'description': self.description,
'created_at': self.created_at.isoformat()
}
if include_tasks:
result['tasks'] = [task.to_dict() for task in self.tasks]
return result

class Task(db.Model):
id = db.Column(db.Integer, primary_key=True)
title = db.Column(db.String(100), nullable=False)
description = db.Column(db.Text)
completed = db.Column(db.Boolean, default=False)
project_id = db.Column(db.Integer, db.ForeignKey('project.id'), nullable=False)
assigned_to = db.Column(db.Integer, db.ForeignKey('user.id'))
created_at = db.Column(db.DateTime, default=datetime.utcnow)

def to_dict(self):
return {
'id': self.id,
'title': self.title,
'description': self.description,
'completed': self.completed,
'project_id': self.project_id,
'assigned_to': self.assigned_to,
'created_at': self.created_at.isoformat()
}

# Initialize DB
with app.app_context():
db.create_all()

# User endpoints
@app.route('/users', methods=['GET', 'POST'])
def users():
if request.method == 'POST':
data = request.get_json()
user = User(
username=data['username'],
email=data['email'],
role=data.get('role', 'developer')
)
db.session.add(user)
db.session.commit()
return jsonify(user.to_dict()), 201

users = User.query.all()
return jsonify([u.to_dict() for u in users])

@app.route('/users/<int:user_id>/tasks')
def user_tasks(user_id):
user = User.query.get_or_404(user_id)
return jsonify([task.to_dict() for task in user.tasks])

# Project endpoints
@app.route('/projects', methods=['GET', 'POST'])
def projects():
if request.method == 'POST':
data = request.get_json()
project = Project(
name=data['name'],
description=data.get('description', '')
)
db.session.add(project)
db.session.commit()
return jsonify(project.to_dict()), 201

projects = Project.query.all()
return jsonify([p.to_dict() for p in projects])

@app.route('/projects/<int:project_id>')
def get_project(project_id):
project = Project.query.get_or_404(project_id)
return jsonify(project.to_dict(include_tasks=True))

@app.route('/projects/<int:project_id>/tasks', methods=['GET', 'POST'])
def project_tasks(project_id):
project = Project.query.get_or_404(project_id)

if request.method == 'POST':
data = request.get_json()
task = Task(
title=data['title'],
description=data.get('description', ''),
project_id=project_id,
assigned_to=data.get('assigned_to')
)
db.session.add(task)
db.session.commit()
return jsonify(task.to_dict()), 201

return jsonify([task.to_dict() for task in project.tasks])

# Task endpoints
@app.route('/tasks/<int:task_id>', methods=['GET', 'PUT', 'DELETE'])
def task_detail(task_id):
task = Task.query.get_or_404(task_id)

if request.method == 'DELETE':
db.session.delete(task)
db.session.commit()
return '', 204

if request.method == 'PUT':
data = request.get_json()
task.title = data.get('title', task.title)
task.description = data.get('description', task.description)
task.completed = data.get('completed', task.completed)
task.assigned_to = data.get('assigned_to', task.assigned_to)
db.session.commit()
return jsonify(task.to_dict())

return jsonify(task.to_dict())

if __name__ == '__main__':
app.run(debug=True)
```

**Testing:**
```bash
# Create user
curl -X POST http://localhost:5000/users \
-H "Content-Type: application/json" \
-d '{"username": "demo_user", "email": "user@company.com", "role": "SRE"}'

# Create project
curl -X POST http://localhost:5000/projects \
-H "Content-Type: application/json" \
-d '{"name": "Platform MCP", "description": "Infrastructure observability"}'

# Create task
curl -X POST http://localhost:5000/projects/1/tasks \
-H "Content-Type: application/json" \
-d '{"title": "Implement new tool", "assigned_to": 1}'

# List project tasks
curl http://localhost:5000/projects/1/tasks

# List user tasks
curl http://localhost:5000/users/1/tasks
```

---

### 2.2 - Authentication & Authorization (3 hours)

**Flask-Login + JWT:**
```bash
pip install flask-login pyjwt bcrypt
```

```python
# auth.py
from flask import Flask, request, jsonify
from flask_sqlalchemy import SQLAlchemy
from werkzeug.security import generate_password_hash, check_password_hash
import jwt
import datetime
from functools import wraps

app = Flask(__name__)
app.config['SECRET_KEY'] = 'your-secret-key-here'
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///users.db'

db = SQLAlchemy(app)

class User(db.Model):
id = db.Column(db.Integer, primary_key=True)
username = db.Column(db.String(50), unique=True, nullable=False)
password_hash = db.Column(db.String(200), nullable=False)
role = db.Column(db.String(20), default='user') # user, admin

def set_password(self, password):
self.password_hash = generate_password_hash(password)

def check_password(self, password):
return check_password_hash(self.password_hash, password)

with app.app_context():
db.create_all()

# JWT decorator
def token_required(f):
@wraps(f)
def decorated(*args, **kwargs):
token = request.headers.get('Authorization')

if not token:
return jsonify({'error': 'Token missing'}), 401

try:
# Remove "Bearer " prefix
if token.startswith('Bearer '):
token = token[7:]

data = jwt.decode(token, app.config['SECRET_KEY'], algorithms=['HS256'])
current_user = User.query.get(data['user_id'])

if not current_user:
return jsonify({'error': 'Invalid token'}), 401

except jwt.ExpiredSignatureError:
return jsonify({'error': 'Token expired'}), 401
except jwt.InvalidTokenError:
return jsonify({'error': 'Invalid token'}), 401

return f(current_user, *args, **kwargs)

return decorated

# Admin required decorator
def admin_required(f):
@wraps(f)
@token_required
def decorated(current_user, *args, **kwargs):
if current_user.role != 'admin':
return jsonify({'error': 'Admin access required'}), 403
return f(current_user, *args, **kwargs)
return decorated

# Auth endpoints
@app.route('/register', methods=['POST'])
def register():
data = request.get_json()

if User.query.filter_by(username=data['username']).first():
return jsonify({'error': 'Username already exists'}), 400

user = User(
username=data['username'],
role=data.get('role', 'user')
)
user.set_password(data['password'])

db.session.add(user)
db.session.commit()

return jsonify({'message': 'User created successfully'}), 201

@app.route('/login', methods=['POST'])
def login():
data = request.get_json()
user = User.query.filter_by(username=data['username']).first()

if not user or not user.check_password(data['password']):
return jsonify({'error': 'Invalid credentials'}), 401

# Generate token (expires in 24 hours)
token = jwt.encode({
'user_id': user.id,
'username': user.username,
'role': user.role,
'exp': datetime.datetime.utcnow() + datetime.timedelta(hours=24)
}, app.config['SECRET_KEY'], algorithm='HS256')

return jsonify({'token': token})

# Protected endpoints
@app.route('/profile')
@token_required
def profile(current_user):
return jsonify({
'username': current_user.username,
'role': current_user.role
})

@app.route('/admin/users')
@admin_required
def admin_users(current_user):
users = User.query.all()
return jsonify([
{'id': u.id, 'username': u.username, 'role': u.role}
for u in users
])

if __name__ == '__main__':
app.run(debug=True)
```

**Exercise 2.2:**
```
Integrate authentication into the Project Management API:
1. Only authenticated users can create projects/tasks
2. Users can only modify their own tasks
3. Admins can see all projects
4. Regular users only see projects where they have assigned tasks

Tip: Combine the @token_required decorator with previous endpoints
```

---

### 2.3 - Flask-Migrate & Alembic (2 hours)

**Database migrations:**
```bash
pip install flask-migrate
```

```python
# app.py
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from flask_migrate import Migrate

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///app.db'

db = SQLAlchemy(app)
migrate = Migrate(app, db)

class User(db.Model):
id = db.Column(db.Integer, primary_key=True)
username = db.Column(db.String(50), unique=True)
email = db.Column(db.String(100), unique=True)
# Later add: created_at = db.Column(db.DateTime, default=datetime.utcnow)
```

**Migration workflow:**
```bash
# Initialize migrations
flask db init

# Create first migration
flask db migrate -m "Initial migration"

# Apply migration
flask db upgrade

# Add new field to model
# Then create new migration:
flask db migrate -m "Add created_at to User"
flask db upgrade

# Rollback
flask db downgrade
```

**Exercise 2.3:**
```
Project: Blog API with migrations

1. First version:
- User (username, email)
- Post (title, content, user_id)

2. Second version (migration):
- User: +created_at, +last_login
- Post: +published, +created_at

3. Third version (migration):
- Comment model (content, user_id, post_id, created_at)
- Post: +view_count

Create a migration for each change and test!
```

---

## Module 3: Advanced Flask (10 hours)

### 3.1 - Blueprints & Application Factory (3 hours)

**Application structure:**
```
flask-app/
├── app/
│  ├── __init__.py     # Application factory
│  ├── models.py      # Database models
│  ├── auth/
│  │  ├── __init__.py
│  │  └── routes.py    # Auth blueprint
│  ├── api/
│  │  ├── __init__.py
│  │  ├── tasks.py     # Tasks blueprint
│  │  └── projects.py   # Projects blueprint
│  └── utils/
│    ├── __init__.py
│    └── decorators.py
├── migrations/
├── tests/
├── config.py
└── run.py
```

**Application factory:**
```python
# app/__init__.py
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from flask_migrate import Migrate

db = SQLAlchemy()
migrate = Migrate()

def create_app(config_name='default'):
app = Flask(__name__)

# Load config
app.config.from_object(f'config.{config_name.capitalize()}Config')

# Initialize extensions
db.init_app(app)
migrate.init_app(app, db)

# Register blueprints
from app.auth import auth_bp
from app.api import api_bp

app.register_blueprint(auth_bp, url_prefix='/auth')
app.register_blueprint(api_bp, url_prefix='/api')

return app
```

```python
# config.py
import os

class Config:
SECRET_KEY = os.environ.get('SECRET_KEY') or 'dev-secret-key'
SQLALCHEMY_TRACK_MODIFICATIONS = False

class DevelopmentConfig(Config):
DEBUG = True
SQLALCHEMY_DATABASE_URI = 'sqlite:///dev.db'

class ProductionConfig(Config):
SQLALCHEMY_DATABASE_URI = os.environ.get('DATABASE_URL')

class TestingConfig(Config):
TESTING = True
SQLALCHEMY_DATABASE_URI = 'sqlite:///test.db'
```

```python
# app/auth/__init__.py
from flask import Blueprint

auth_bp = Blueprint('auth', __name__)

from . import routes
```

```python
# app/auth/routes.py
from flask import request, jsonify
from app.auth import auth_bp
from app.models import User
from app import db

@auth_bp.route('/register', methods=['POST'])
def register():
data = request.get_json()
# ... registration logic
return jsonify({'message': 'User created'}), 201

@auth_bp.route('/login', methods=['POST'])
def login():
# ... login logic
return jsonify({'token': 'jwt-token'})
```

```python
# run.py
from app import create_app
import os

app = create_app(os.environ.get('FLASK_ENV', 'development'))

if __name__ == '__main__':
app.run()
```

**Exercise 3.1:**
```
Refactor the Project Management API to use blueprints:

Blueprints:
- auth_bp: /auth/register, /auth/login
- users_bp: /api/users/*
- projects_bp: /api/projects/*
- tasks_bp: /api/tasks/*

Structure:
app/
├── __init__.py
├── models.py
├── auth/
├── users/
├── projects/
└── tasks/

Each blueprint with its own routes.py
```

---

### 3.2 - Error Handling & Logging (2 hours)

**Custom error handlers:**
```python
# app/errors.py
from flask import jsonify
from werkzeug.exceptions import HTTPException

def register_error_handlers(app):
@app.errorhandler(404)
def not_found(error):
return jsonify({'error': 'Resource not found'}), 404

@app.errorhandler(400)
def bad_request(error):
return jsonify({'error': 'Bad request', 'message': str(error)}), 400

@app.errorhandler(500)
def internal_error(error):
app.logger.error(f'Server Error: {error}')
return jsonify({'error': 'Internal server error'}), 500

@app.errorhandler(Exception)
def handle_exception(e):
# Pass through HTTP errors
if isinstance(e, HTTPException):
return e

# Log the error
app.logger.exception('Unhandled exception')

return jsonify({'error': 'An unexpected error occurred'}), 500
```

**Logging setup:**
```python
# app/__init__.py
import logging
from logging.handlers import RotatingFileHandler
import os

def create_app(config_name='default'):
app = Flask(__name__)
# ... config loading

# Setup logging
if not app.debug and not app.testing:
if not os.path.exists('logs'):
os.mkdir('logs')

file_handler = RotatingFileHandler(
'logs/flask-app.log',
maxBytes=10240000, # 10MB
backupCount=10
)
file_handler.setFormatter(logging.Formatter(
'[%(asctime)s] %(levelname)s in %(module)s: %(message)s'
))
file_handler.setLevel(logging.INFO)
app.logger.addHandler(file_handler)

app.logger.setLevel(logging.INFO)
app.logger.info('Flask app startup')

# Register error handlers
from app.errors import register_error_handlers
register_error_handlers(app)

return app
```

**Structured logging:**
```python
# app/utils/logging.py
import logging
import json

class JSONFormatter(logging.Formatter):
def format(self, record):
log_record = {
'timestamp': self.formatTime(record),
'level': record.levelname,
'module': record.module,
'message': record.getMessage(),
}

if record.exc_info:
log_record['exception'] = self.formatException(record.exc_info)

return json.dumps(log_record)

# Usage in routes:
from flask import current_app

@api_bp.route('/tasks', methods=['POST'])
def create_task():
try:
data = request.get_json()
current_app.logger.info('Creating task', extra={'data': data})
# ... create task
current_app.logger.info('Task created', extra={'task_id': task.id})
return jsonify(task.to_dict()), 201
except Exception as e:
current_app.logger.error('Failed to create task', exc_info=True)
raise
```

**Exercise 3.2:**
```
Implement comprehensive error handling and logging:

1. Custom exceptions:
- ResourceNotFoundError
- ValidationError
- AuthorizationError

2. Error handlers for each

3. Request/Response logging middleware:
- Log every request: method, path, duration
- Log response status
- Log user_id if authenticated

4. Structured JSON logging in production

5. Correlation ID for every request
```

---

### 3.3 - Testing (3 hours)

**Pytest setup:**
```bash
pip install pytest pytest-flask pytest-cov
```

```python
# tests/conftest.py
import pytest
from app import create_app, db
from app.models import User, Project, Task

@pytest.fixture
def app():
app = create_app('testing')

with app.app_context():
db.create_all()
yield app
db.session.remove()
db.drop_all()

@pytest.fixture
def client(app):
return app.test_client()

@pytest.fixture
def runner(app):
return app.test_cli_runner()

@pytest.fixture
def auth_headers(client):
# Create test user and get token
client.post('/auth/register', json={
'username': 'testuser',
'password': 'testpass'
})

response = client.post('/auth/login', json={
'username': 'testuser',
'password': 'testpass'
})

token = response.get_json()['token']
return {'Authorization': f'Bearer {token}'}
```

```python
# tests/test_auth.py
def test_register(client):
response = client.post('/auth/register', json={
'username': 'newuser',
'password': 'password123'
})

assert response.status_code == 201
assert 'message' in response.get_json()

def test_login(client):
# First register
client.post('/auth/register', json={
'username': 'testuser',
'password': 'testpass'
})

# Then login
response = client.post('/auth/login', json={
'username': 'testuser',
'password': 'testpass'
})

assert response.status_code == 200
assert 'token' in response.get_json()

def test_login_invalid_credentials(client):
response = client.post('/auth/login', json={
'username': 'nonexistent',
'password': 'wrong'
})

assert response.status_code == 401
```

```python
# tests/test_tasks.py
def test_create_task(client, auth_headers):
# First create a project
response = client.post('/api/projects',
json={'name': 'Test Project'},
headers=auth_headers
)
project_id = response.get_json()['id']

# Create task
response = client.post(f'/api/projects/{project_id}/tasks',
json={'title': 'Test Task'},
headers=auth_headers
)

assert response.status_code == 201
data = response.get_json()
assert data['title'] == 'Test Task'
assert data['project_id'] == project_id

def test_list_tasks_requires_auth(client):
response = client.get('/api/tasks')
assert response.status_code == 401

def test_update_task(client, auth_headers):
# Create task first
# ... setup

# Update
response = client.put('/api/tasks/1',
json={'completed': True},
headers=auth_headers
)

assert response.status_code == 200
assert response.get_json()['completed'] is True
```

**Run tests:**
```bash
# Run all tests
pytest

# With coverage
pytest --cov=app --cov-report=html

# Specific test file
pytest tests/test_auth.py

# Verbose
pytest -v

# Stop on first failure
pytest -x
```

**Exercise 3.3:**
```
Write comprehensive test suite for Project Management API:

1. Unit tests:
- Model methods (to_dict, validations)
- Utility functions

2. Integration tests:
- Auth flow (register, login, protected routes)
- CRUD operations for all resources
- Permissions (user vs admin)
- Edge cases (404, 400, 403)

3. Test fixtures:
- Sample users (regular, admin)
- Sample projects with tasks
- Helper functions

Goal: >80% code coverage
```

---

### 3.4 - Background Tasks & Celery (2 hours)

**Celery setup:**
```bash
pip install celery redis
```

```python
# app/celery.py
from celery import Celery

def make_celery(app):
celery = Celery(
app.import_name,
backend=app.config['CELERY_RESULT_BACKEND'],
broker=app.config['CELERY_BROKER_URL']
)
celery.conf.update(app.config)

class ContextTask(celery.Task):
def __call__(self, *args, **kwargs):
with app.app_context():
return self.run(*args, **kwargs)

celery.Task = ContextTask
return celery
```

```python
# config.py
class Config:
# ... existing config
CELERY_BROKER_URL = 'redis://localhost:6379/0'
CELERY_RESULT_BACKEND = 'redis://localhost:6379/0'
```

```python
# app/tasks.py
from app import celery, db
from app.models import User, Report
import time

@celery.task
def send_email(user_id, subject, body):
user = User.query.get(user_id)
# Simulate email sending
time.sleep(2)
print(f'Email sent to {user.email}: {subject}')
return f'Email sent to {user.email}'

@celery.task(bind=True)
def generate_report(self, project_id):
# Update task progress
self.update_state(state='PROGRESS', meta={'current': 0, 'total': 100})

project = Project.query.get(project_id)
tasks = project.tasks

# Simulate heavy processing
for i in range(100):
time.sleep(0.1)
self.update_state(state='PROGRESS', meta={'current': i + 1, 'total': 100})

# Generate report
report = Report(
project_id=project_id,
total_tasks=len(tasks),
completed_tasks=len([t for t in tasks if t.completed])
)
db.session.add(report)
db.session.commit()

return {'report_id': report.id}
```

```python
# app/api/tasks.py (routes)
from app.tasks import send_email, generate_report

@api_bp.route('/tasks/<int:task_id>/assign', methods=['POST'])
@token_required
def assign_task(current_user, task_id):
data = request.get_json()
task = Task.query.get_or_404(task_id)
user = User.query.get_or_404(data['user_id'])

task.assigned_to = user.id
db.session.commit()

# Send email asynchronously
send_email.delay(
user.id,
f'New task assigned: {task.title}',
f'You have been assigned task: {task.title}'
)

return jsonify(task.to_dict())

@api_bp.route('/projects/<int:project_id>/report', methods=['POST'])
@token_required
def create_report(current_user, project_id):
# Start background task
task = generate_report.delay(project_id)

return jsonify({
'task_id': task.id,
'status_url': f'/api/tasks/status/{task.id}'
}), 202

@api_bp.route('/tasks/status/<task_id>')
@token_required
def task_status(current_user, task_id):
task = generate_report.AsyncResult(task_id)

if task.state == 'PENDING':
response = {'state': 'PENDING', 'current': 0, 'total': 100}
elif task.state == 'PROGRESS':
response = {
'state': 'PROGRESS',
'current': task.info.get('current', 0),
'total': task.info.get('total', 100)
}
elif task.state == 'SUCCESS':
response = {'state': 'SUCCESS', 'result': task.result}
else:
response = {'state': task.state}

return jsonify(response)
```

**Run Celery worker:**
```bash
# Start Redis
redis-server

# Start Celery worker
celery -A app.celery worker --loglevel=info
```

**Exercise 3.4:**
```
Implement background tasks:

1. Email notifications:
- Task assigned
- Task completed
- Project deadline approaching

2. Report generation:
- Weekly project summary
- User activity report
- Progress tracking with status endpoint

3. Periodic tasks (Celery Beat):
- Daily reminder for overdue tasks
- Weekly inactive user cleanup

4. Test async tasks:
- Mock email sending
- Test task status polling
```

---

## Module 4: Real-World Project (12 hours)

### Project: Platform Investigation Tracker API

**Requirements:**

**1. Models:**
```python
- User (username, email, role: SRE/Developer/Manager)
- Investigation (ticket_key, title, status, priority, assigned_to, created_at)
- Finding (investigation_id, description, severity, created_at)
- Action (investigation_id, description, completed, assigned_to, due_date)
- Comment (investigation_id, user_id, content, created_at)
```

**2. Features:**
- Full CRUD for all models
- JWT authentication
- Role-based permissions (SRE can assign, Manager can view all)
- Search & filters (by status, priority, assigned user)
- Background task: Send daily summary email
- File upload: Attach logs to investigations
- Metrics endpoint: GET /api/metrics (investigation stats)

**3. Advanced:**
- Pagination (query params: page, per_page)
- Sorting (query param: sort=created_at:desc)
- Caching (Redis): Cache investigation list for 5 minutes
- Rate limiting: 100 requests per hour per user
- WebSocket: Real-time investigation updates (Flask-SocketIO)

**4. Deployment:**
- Dockerfile
- docker-compose (Flask + PostgreSQL + Redis + Celery)
- Environment variables
- Production config (Gunicorn)

**Implementation Guide:**

**Step 1: Project structure (1 hour)**
```
investigation-tracker/
├── app/
│  ├── __init__.py
│  ├── models.py
│  ├── auth/
│  ├── api/
│  │  ├── investigations.py
│  │  ├── findings.py
│  │  ├── actions.py
│  │  └── metrics.py
│  ├── tasks.py
│  └── utils/
├── migrations/
├── tests/
├── config.py
├── Dockerfile
├── docker-compose.yml
├── requirements.txt
└── run.py
```

**Step 2: Models & Database (2 hours)**
- Implement all models
- Relationships: One-to-many, Many-to-one
- Validation methods
- to_dict() methods
- Initial migration

**Step 3: Authentication & Authorization (2 hours)**
- JWT-based auth
- Role-based decorators
- Permission checks

**Step 4: CRUD Endpoints (3 hours)**
- Investigations CRUD
- Findings, Actions, Comments CRUD
- Filtering, sorting, pagination
- Error handling

**Step 5: Advanced Features (2 hours)**
- File upload (Flask-Uploads)
- Metrics endpoint
- Caching with Redis
- Rate limiting

**Step 6: Background Tasks (1 hour)**
- Daily summary email
- Overdue action reminders

**Step 7: Testing (1 hour)**
- Integration tests
- Coverage >80%

**Step 8: Deployment (1 hour)**
- Docker setup
- Production config
- Documentation

---

## Module 5: Flask Best Practices & Tips (5 hours)

### 5.1 - Security Best Practices

```python
# 1. HTTPS only in production
from flask_talisman import Talisman

if not app.debug:
Talisman(app, force_https=True)

# 2. CORS configuration
from flask_cors import CORS

CORS(app, resources={
r"/api/*": {
"origins": ["https://yourdomain.com"],
"methods": ["GET", "POST", "PUT", "DELETE"],
"allow_headers": ["Content-Type", "Authorization"]
}
})

# 3. Input validation
from marshmallow import Schema, fields, validate

class TaskSchema(Schema):
title = fields.Str(required=True, validate=validate.Length(min=1, max=100))
description = fields.Str(validate=validate.Length(max=500))
completed = fields.Bool()

schema = TaskSchema()
errors = schema.validate(request.get_json())
if errors:
return jsonify({'errors': errors}), 400

# 4. SQL injection prevention (use ORM)
# WRONG:
db.execute(f"SELECT * FROM users WHERE username = '{username}'")

# RIGHT:
User.query.filter_by(username=username).first()

# 5. Rate limiting
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address

limiter = Limiter(
app,
key_func=get_remote_address,
default_limits=["200 per day", "50 per hour"]
)

@app.route('/api/expensive-operation')
@limiter.limit("10 per minute")
def expensive():
pass

# 6. Secret management
# WRONG:
app.config['SECRET_KEY'] = 'hardcoded-secret'

# RIGHT:
app.config['SECRET_KEY'] = os.environ.get('SECRET_KEY') or secrets.token_hex(32)
```

### 5.2 - Performance Optimization

```python
# 1. Database query optimization
# WRONG (N+1 problem):
projects = Project.query.all()
for project in projects:
print(project.tasks) # Triggers separate query for each project

# RIGHT (eager loading):
projects = Project.query.options(db.joinedload(Project.tasks)).all()

# 2. Caching
from flask_caching import Cache

cache = Cache(app, config={'CACHE_TYPE': 'redis'})

@app.route('/api/heavy-operation')
@cache.cached(timeout=300) # Cache for 5 minutes
def heavy_operation():
# Expensive computation
return jsonify(result)

# Cache with key function
@cache.cached(timeout=60, key_prefix=lambda: f'user_{current_user.id}_tasks')
def get_user_tasks():
return Task.query.filter_by(user_id=current_user.id).all()

# 3. Database connection pooling
app.config['SQLALCHEMY_POOL_SIZE'] = 10
app.config['SQLALCHEMY_POOL_RECYCLE'] = 3600

# 4. Compression
from flask_compress import Compress
Compress(app)

# 5. Async operations for I/O
# Use Celery for heavy tasks, not blocking the request thread
```

### 5.3 - Monitoring & Observability

```python
# 1. Prometheus metrics
from prometheus_flask_exporter import PrometheusMetrics

metrics = PrometheusMetrics(app)

# Custom metrics
task_counter = metrics.counter(
'tasks_created_total',
'Total tasks created',
labels={'status': lambda: 'completed'}
)

@app.route('/tasks', methods=['POST'])
def create_task():
# ... create task
task_counter.inc()
return jsonify(task.to_dict())

# 2. Health checks
@app.route('/health')
def health():
try:
# Check database
db.session.execute('SELECT 1')

# Check Redis
cache.get('health_check')

return jsonify({
'status': 'healthy',
'database': 'ok',
'cache': 'ok'
})
except Exception as e:
return jsonify({
'status': 'unhealthy',
'error': str(e)
}), 503

# 3. Request tracing
import uuid
from flask import g

@app.before_request
def before_request():
g.request_id = request.headers.get('X-Request-ID') or str(uuid.uuid4())

@app.after_request
def after_request(response):
response.headers['X-Request-ID'] = g.request_id
return response

# 4. Structured logging with request context
@app.route('/api/tasks', methods=['POST'])
def create_task():
app.logger.info('Creating task', extra={
'request_id': g.request_id,
'user_id': current_user.id,
'endpoint': request.endpoint
})
```

---

## Useful Resources

**Documentation:**
- Official Flask docs: https://flask.palletsprojects.com/
- Flask-SQLAlchemy: https://flask-sqlalchemy.palletsprojects.com/
- Flask-Migrate: https://flask-migrate.readthedocs.io/

**Tutorials:**
- Miguel Grinberg's Flask Mega-Tutorial
- Real Python Flask articles
- Full Stack Python - Flask

**Extensions:**
- Flask-RESTful - REST API framework
- Flask-Marshmallow - Serialization/deserialization
- Flask-CORS - CORS handling
- Flask-Limiter - Rate limiting
- Flask-Caching - Caching
- Flask-SocketIO - WebSockets

**Books:**
- "Flask Web Development" by Miguel Grinberg
- "Building RESTful Python Web Services" by Gaston C. Hillar

---

## Next Steps

1. **Practice Flask basics** (Module 1-2): 1-2 weeks
2. **Build a real project** (Module 4): 2-3 weeks
3. **Learn Django** (understand differences)
4. **Learn FastAPI** (modern async approach)
5. **Compare all three frameworks** - when to use each

**Project Ideas:**
- Platform MCP Dashboard (Flask + HTMX)
- Issue Investigation Tracker
- SOP Documentation API
- Infrastructure Health Dashboard

---

**Last Updated:** 2026-03-22
