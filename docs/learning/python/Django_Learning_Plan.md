# Django Learning Plan - Complete Guide

**Created:** 2026-03-22
**Target Audience:** Personal learning path
**Time Required:** ~60-70 hours (10-12 weeks, 6 hours per week)

---

## Why Django?

**Advantages:**
- "Batteries included" - everything built-in (admin, auth, ORM)
- Rapid development - quick MVP creation
- Secure by default (CSRF, XSS protection)
- Scalable - for large enterprise applications
- Django Admin - free admin interface

**When to use:**
- Complex web applications
- Admin panel needed
- Content management systems
- Fast MVP where many features needed
- Enterprise applications

**Flask vs Django:**
- Flask: micro, flexible, only what you ask for
- Django: monolithic, everything built-in, structured

---

## Module 1: Django Fundamentals (8 hours)

### 1.1 - Django Project Setup (2 hours)

**Installation:**
```bash
mkdir -p ~/learning/django-tutorial
cd ~/learning/django-tutorial
python -m venv venv
source venv/bin/activate
pip install django
```

**Project creation:**
```bash
# Create project
django-admin startproject myproject
cd myproject

# Project structure:
myproject/
├── manage.py      # CLI tool
└── myproject/
├── __init__.py
├── settings.py   # Configuration
├── urls.py     # URL routing
├── asgi.py     # ASGI config
└── wsgi.py     # WSGI config

# Run development server
python manage.py runserver

# Visit: http://localhost:8000
```

**App creation:**
```bash
# Create app
python manage.py startapp tasks

# App structure:
tasks/
├── __init__.py
├── admin.py     # Admin config
├── apps.py      # App config
├── models.py     # Database models
├── views.py     # View functions/classes
├── urls.py      # (you create) URL patterns
├── tests.py     # Tests
└── migrations/    # Database migrations
```

**Register app:**
```python
# myproject/settings.py
INSTALLED_APPS = [
'django.contrib.admin',
'django.contrib.auth',
'django.contrib.contenttypes',
'django.contrib.sessions',
'django.contrib.messages',
'django.contrib.staticfiles',
'tasks', # Add your app
]
```

**Exercise 1.1:**
```bash
# Exercise: Blog project setup
1. Create new Django project named "blog_project"
2. Create "blog" app
3. Register app in settings.py
4. Run the dev server
5. Navigate to the admin interface (/admin)
```

---

### 1.2 - Models & ORM (3 hours)

**Defining models:**
```python
# tasks/models.py
from django.db import models
from django.contrib.auth.models import User

class Task(models.Model):
STATUS_CHOICES = [
('TODO', 'To Do'),
('IN_PROGRESS', 'In Progress'),
('DONE', 'Done'),
]

title = models.CharField(max_length=200)
description = models.TextField(blank=True)
status = models.CharField(max_length=20, choices=STATUS_CHOICES, default='TODO')
priority = models.IntegerField(default=0)
assigned_to = models.ForeignKey(
User,
on_delete=models.SET_NULL,
null=True,
blank=True,
related_name='tasks'
)
created_at = models.DateTimeField(auto_now_add=True)
updated_at = models.DateTimeField(auto_now=True)

class Meta:
ordering = ['-created_at']
verbose_name = 'Task'
verbose_name_plural = 'Tasks'

def __str__(self):
return self.title

def is_completed(self):
return self.status == 'DONE'
```

**Migrations:**
```bash
# Create migration
python manage.py makemigrations

# Apply migration
python manage.py migrate

# Show SQL
python manage.py sqlmigrate tasks 0001

# Interactive shell
python manage.py shell
```

**ORM Queries:**
```python
# Django shell: python manage.py shell

from tasks.models import Task
from django.contrib.auth.models import User

# Create
task = Task.objects.create(
title="Learn Django",
description="Complete Django tutorial",
priority=1
)

# Read
all_tasks = Task.objects.all()
task = Task.objects.get(id=1)
tasks = Task.objects.filter(status='TODO')
tasks = Task.objects.filter(priority__gte=1) # priority >= 1

# Update
task.status = 'IN_PROGRESS'
task.save()

# Or bulk update:
Task.objects.filter(status='TODO').update(priority=2)

# Delete
task.delete()
Task.objects.filter(status='DONE').delete()

# Relationships
user = User.objects.get(username='demo_user')
task.assigned_to = user
task.save()

# Reverse relationship
user_tasks = user.tasks.all()

# Aggregation
from django.db.models import Count, Avg
Task.objects.aggregate(total=Count('id'), avg_priority=Avg('priority'))

# Complex queries
tasks = Task.objects.filter(
status='IN_PROGRESS'
).exclude(
priority=0
).order_by('-priority')[:10]
```

**Exercise 1.2:**
```python
# Blog Models
# Implement the following models:

# 1. Category
class Category(models.Model):
name = models.CharField(max_length=100, unique=True)
slug = models.SlugField(unique=True)
description = models.TextField(blank=True)

# 2. Post
class Post(models.Model):
title = models.CharField(max_length=200)
slug = models.SlugField(unique=True)
content = models.TextField()
author = models.ForeignKey(User, on_delete=models.CASCADE)
category = models.ForeignKey(Category, on_delete=models.SET_NULL, null=True)
published = models.BooleanField(default=False)
published_date = models.DateTimeField(null=True, blank=True)
created_at = models.DateTimeField(auto_now_add=True)
updated_at = models.DateTimeField(auto_now=True)

# 3. Comment
class Comment(models.Model):
post = models.ForeignKey(Post, on_delete=models.CASCADE, related_name='comments')
author = models.ForeignKey(User, on_delete=models.CASCADE)
content = models.TextField()
created_at = models.DateTimeField(auto_now_add=True)
approved = models.BooleanField(default=False)

# 4. Tag (Many-to-Many)
class Tag(models.Model):
name = models.CharField(max_length=50, unique=True)
posts = models.ManyToManyField(Post, related_name='tags')

# Queries to practice:
# - Get all published posts
# - Get posts by category
# - Get posts with comments count
# - Get most commented posts
# - Get posts by tag
```

---

### 1.3 - Views & URLs (3 hours)

**Function-Based Views (FBV):**
```python
# tasks/views.py
from django.shortcuts import render, get_object_or_404, redirect
from django.http import HttpResponse, JsonResponse
from .models import Task

def task_list(request):
tasks = Task.objects.all()
return render(request, 'tasks/task_list.html', {'tasks': tasks})

def task_detail(request, task_id):
task = get_object_or_404(Task, id=task_id)
return render(request, 'tasks/task_detail.html', {'task': task})

def task_create(request):
if request.method == 'POST':
title = request.POST.get('title')
description = request.POST.get('description')
task = Task.objects.create(title=title, description=description)
return redirect('task_detail', task_id=task.id)
return render(request, 'tasks/task_form.html')

def task_update(request, task_id):
task = get_object_or_404(Task, id=task_id)
if request.method == 'POST':
task.title = request.POST.get('title')
task.description = request.POST.get('description')
task.status = request.POST.get('status')
task.save()
return redirect('task_detail', task_id=task.id)
return render(request, 'tasks/task_form.html', {'task': task})

def task_delete(request, task_id):
task = get_object_or_404(Task, id=task_id)
if request.method == 'POST':
task.delete()
return redirect('task_list')
return render(request, 'tasks/task_confirm_delete.html', {'task': task})

# API views (JSON responses)
def api_task_list(request):
tasks = Task.objects.all()
data = [
{
'id': task.id,
'title': task.title,
'status': task.status,
'created_at': task.created_at.isoformat()
}
for task in tasks
]
return JsonResponse({'tasks': data})
```

**URL Configuration:**
```python
# tasks/urls.py
from django.urls import path
from . import views

app_name = 'tasks'

urlpatterns = [
path('', views.task_list, name='task_list'),
path('<int:task_id>/', views.task_detail, name='task_detail'),
path('create/', views.task_create, name='task_create'),
path('<int:task_id>/update/', views.task_update, name='task_update'),
path('<int:task_id>/delete/', views.task_delete, name='task_delete'),

# API endpoints
path('api/tasks/', views.api_task_list, name='api_task_list'),
]
```

```python
# myproject/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
path('admin/', admin.site.urls),
path('tasks/', include('tasks.urls')),
]
```

**Templates:**
```html
<!-- templates/tasks/task_list.html -->
{% extends 'base.html' %}

{% block title %}Tasks{% endblock %}

{% block content %}
<h1>My Tasks</h1>

<a href="{% url 'tasks:task_create' %}" class="btn">New Task</a>

<ul>
{% for task in tasks %}
<li>
<a href="{% url 'tasks:task_detail' task.id %}">{{ task.title }}</a>
<span class="badge">{{ task.get_status_display }}</span>
</li>
{% empty %}
<li>No tasks yet.</li>
{% endfor %}
</ul>
{% endblock %}
```

```html
<!-- templates/tasks/task_detail.html -->
{% extends 'base.html' %}

{% block title %}{{ task.title }}{% endblock %}

{% block content %}
<h1>{{ task.title }}</h1>
<p>{{ task.description }}</p>
<p>Status: {{ task.get_status_display }}</p>
<p>Created: {{ task.created_at }}</p>

<a href="{% url 'tasks:task_update' task.id %}">Edit</a>
<a href="{% url 'tasks:task_delete' task.id %}">Delete</a>
{% endblock %}
```

**Exercise 1.3:**
```python
# Blog Views
# Implement the following views:

# 1. post_list - all published posts
# 2. post_detail - one post in detail (by slug)
# 3. post_by_category - filter by category
# 4. post_by_tag - filter by tag
# 5. comment_create - add comment to POST

# Bonus: Pagination
from django.core.paginator import Paginator

def post_list(request):
posts = Post.objects.filter(published=True)
paginator = Paginator(posts, 10) # 10 per page
page = request.GET.get('page')
posts = paginator.get_page(page)
return render(request, 'blog/post_list.html', {'posts': posts})
```

---

## Module 2: Forms & Admin (8 hours)

### 2.1 - Django Forms (4 hours)

**ModelForm:**
```python
# tasks/forms.py
from django import forms
from .models import Task

class TaskForm(forms.ModelForm):
class Meta:
model = Task
fields = ['title', 'description', 'status', 'priority', 'assigned_to']
widgets = {
'description': forms.Textarea(attrs={'rows': 4}),
'title': forms.TextInput(attrs={'placeholder': 'Task title'}),
}

def clean_title(self):
title = self.cleaned_data.get('title')
if len(title) < 5:
raise forms.ValidationError('Title must be at least 5 characters')
return title

def clean(self):
cleaned_data = super().clean()
status = cleaned_data.get('status')
assigned_to = cleaned_data.get('assigned_to')

if status == 'IN_PROGRESS' and not assigned_to:
raise forms.ValidationError('Task must be assigned to progress')

return cleaned_data
```

**Using Forms in Views:**
```python
# tasks/views.py
from .forms import TaskForm

def task_create(request):
if request.method == 'POST':
form = TaskForm(request.POST)
if form.is_valid():
task = form.save()
return redirect('tasks:task_detail', task_id=task.id)
else:
form = TaskForm()

return render(request, 'tasks/task_form.html', {'form': form})

def task_update(request, task_id):
task = get_object_or_404(Task, id=task_id)

if request.method == 'POST':
form = TaskForm(request.POST, instance=task)
if form.is_valid():
form.save()
return redirect('tasks:task_detail', task_id=task.id)
else:
form = TaskForm(instance=task)

return render(request, 'tasks/task_form.html', {
'form': form,
'task': task
})
```

**Form Template:**
```html
<!-- templates/tasks/task_form.html -->
{% extends 'base.html' %}

{% block content %}
<h1>{% if task %}Edit{% else %}Create{% endif %} Task</h1>

<form method="post">
{% csrf_token %}

{% if form.errors %}
<div class="errors">
{{ form.errors }}
</div>
{% endif %}

{{ form.as_p }}

<button type="submit">Save</button>
<a href="{% url 'tasks:task_list' %}">Cancel</a>
</form>
{% endblock %}
```

**Custom Forms (non-model):**
```python
# tasks/forms.py
class TaskFilterForm(forms.Form):
status = forms.ChoiceField(
choices=[('', 'All')] + Task.STATUS_CHOICES,
required=False
)
priority_min = forms.IntegerField(min_value=0, required=False)
assigned_to = forms.ModelChoiceField(
queryset=User.objects.all(),
required=False,
empty_label='Anyone'
)
search = forms.CharField(max_length=100, required=False)

# View with form
def task_list(request):
form = TaskFilterForm(request.GET)
tasks = Task.objects.all()

if form.is_valid():
if form.cleaned_data.get('status'):
tasks = tasks.filter(status=form.cleaned_data['status'])
if form.cleaned_data.get('priority_min'):
tasks = tasks.filter(priority__gte=form.cleaned_data['priority_min'])
if form.cleaned_data.get('assigned_to'):
tasks = tasks.filter(assigned_to=form.cleaned_data['assigned_to'])
if form.cleaned_data.get('search'):
tasks = tasks.filter(title__icontains=form.cleaned_data['search'])

return render(request, 'tasks/task_list.html', {
'tasks': tasks,
'filter_form': form
})
```

**Exercise 2.1:**
```python
# Blog Forms
# Create forms:

# 1. PostForm - for creating new blog post
#  - Auto-generate slug from title
#  - Only published users can publish

# 2. CommentForm - for adding comment
#  - Email validation
#  - Content min 10 characters

# 3. PostSearchForm - search and filtering
#  - Search in title and content
#  - Category filter
#  - Date range filter (published_date)
#  - Tags filter (multiple selection)

# Implement views and templates too!
```

---

### 2.2 - Django Admin (4 hours)

**Register Models:**
```python
# tasks/admin.py
from django.contrib import admin
from .models import Task

@admin.register(Task)
class TaskAdmin(admin.ModelAdmin):
list_display = ['title', 'status', 'priority', 'assigned_to', 'created_at']
list_filter = ['status', 'priority', 'created_at']
search_fields = ['title', 'description']
ordering = ['-created_at']
date_hierarchy = 'created_at'

fieldsets = [
('Basic Info', {
'fields': ['title', 'description']
}),
('Details', {
'fields': ['status', 'priority', 'assigned_to']
}),
('Metadata', {
'fields': ['created_at', 'updated_at'],
'classes': ['collapse']
}),
]

readonly_fields = ['created_at', 'updated_at']

# Inline related objects
# inlines = [CommentInline]

# Custom actions
actions = ['mark_as_done', 'increase_priority']

def mark_as_done(self, request, queryset):
updated = queryset.update(status='DONE')
self.message_user(request, f'{updated} tasks marked as done.')
mark_as_done.short_description = 'Mark selected as Done'

def increase_priority(self, request, queryset):
for task in queryset:
task.priority += 1
task.save()
self.message_user(request, 'Priority increased.')

# Custom queryset
def get_queryset(self, request):
qs = super().get_queryset(request)
if request.user.is_superuser:
return qs
return qs.filter(assigned_to=request.user)
```

**Inline Admin:**
```python
# blog/models.py
class Comment(models.Model):
post = models.ForeignKey(Post, on_delete=models.CASCADE, related_name='comments')
# ... other fields

# blog/admin.py
class CommentInline(admin.TabularInline):
model = Comment
extra = 1
fields = ['author', 'content', 'approved']

@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
inlines = [CommentInline]
prepopulated_fields = {'slug': ('title',)}
list_display = ['title', 'author', 'category', 'published', 'created_at']
list_filter = ['published', 'category', 'created_at']
search_fields = ['title', 'content']
```

**Custom Admin Actions:**
```python
@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
actions = ['publish_posts', 'unpublish_posts']

def publish_posts(self, request, queryset):
from django.utils import timezone
updated = queryset.update(published=True, published_date=timezone.now())
self.message_user(request, f'{updated} posts published.')

def unpublish_posts(self, request, queryset):
updated = queryset.update(published=False)
self.message_user(request, f'{updated} posts unpublished.')
```

**Custom Admin Dashboard:**
```python
# myproject/admin.py
from django.contrib import admin

admin.site.site_header = "Platform Task Manager"
admin.site.site_title = "Task Admin"
admin.site.index_title = "Welcome to Task Management"
```

**Exercise 2.2:**
```python
# Blog Admin Customization

# Implement:
# 1. PostAdmin
#  - List display: title, author, category, comment count, published
#  - List filters: published, category, tags
#  - Search: title, content
#  - Prepopulated slug
#  - CommentInline
#  - Custom action: bulk publish/unpublish

# 2. CommentAdmin
#  - List display: post, author, content preview, approved
#  - List filter: approved, created_at
#  - Action: approve comments

# 3. CategoryAdmin
#  - Prepopulated slug
#  - Show post count

# 4. Custom dashboard widget showing:
#  - Total posts
#  - Published posts
#  - Pending comments
```

---

## Module 3: Authentication & Permissions (8 hours)

### 3.1 - Built-in Authentication (4 hours)

**User Registration:**
```python
# accounts/forms.py
from django import forms
from django.contrib.auth.forms import UserCreationForm
from django.contrib.auth.models import User

class SignUpForm(UserCreationForm):
email = forms.EmailField(required=True)
first_name = forms.CharField(max_length=30, required=False)
last_name = forms.CharField(max_length=30, required=False)

class Meta:
model = User
fields = ['username', 'email', 'first_name', 'last_name', 'password1', 'password2']

# accounts/views.py
from django.contrib.auth import login
from django.shortcuts import render, redirect
from .forms import SignUpForm

def signup(request):
if request.method == 'POST':
form = SignUpForm(request.POST)
if form.is_valid():
user = form.save()
login(request, user)
return redirect('tasks:task_list')
else:
form = SignUpForm()
return render(request, 'accounts/signup.html', {'form': form})
```

**Login/Logout URLs:**
```python
# myproject/urls.py
from django.urls import path, include
from django.contrib.auth import views as auth_views

urlpatterns = [
path('admin/', admin.site.urls),
path('accounts/login/', auth_views.LoginView.as_view(template_name='accounts/login.html'), name='login'),
path('accounts/logout/', auth_views.LogoutView.as_view(), name='logout'),
path('accounts/signup/', signup, name='signup'),
path('tasks/', include('tasks.urls')),
]

# settings.py
LOGIN_REDIRECT_URL = '/tasks/'
LOGOUT_REDIRECT_URL = '/accounts/login/'
```

**Login Required Decorator:**
```python
# tasks/views.py
from django.contrib.auth.decorators import login_required

@login_required
def task_create(request):
# Only authenticated users can access
pass

@login_required
def task_update(request, task_id):
task = get_object_or_404(Task, id=task_id)

# Check ownership
if task.assigned_to != request.user and not request.user.is_staff:
raise PermissionDenied

# ... update logic
```

**Password Reset:**
```python
# myproject/urls.py
urlpatterns = [
# Password reset flow
path('accounts/password_reset/',
auth_views.PasswordResetView.as_view(template_name='accounts/password_reset.html'),
name='password_reset'),

path('accounts/password_reset/done/',
auth_views.PasswordResetDoneView.as_view(template_name='accounts/password_reset_done.html'),
name='password_reset_done'),

path('accounts/reset/<uidb64>/<token>/',
auth_views.PasswordResetConfirmView.as_view(template_name='accounts/password_reset_confirm.html'),
name='password_reset_confirm'),

path('accounts/reset/done/',
auth_views.PasswordResetCompleteView.as_view(template_name='accounts/password_reset_complete.html'),
name='password_reset_complete'),
]

# settings.py (email configuration for password reset)
EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend' # Dev: print to console
# Production: use SMTP
```

**Exercise 3.1:**
```python
# User Profile Extension

# 1. Create UserProfile model
class UserProfile(models.Model):
user = models.OneToOneField(User, on_delete=models.CASCADE, related_name='profile')
bio = models.TextField(blank=True)
avatar = models.ImageField(upload_to='avatars/', blank=True)
location = models.CharField(max_length=100, blank=True)
website = models.URLField(blank=True)

# 2. Signal to auto-create profile
from django.db.models.signals import post_save
from django.dispatch import receiver

@receiver(post_save, sender=User)
def create_user_profile(sender, instance, created, **kwargs):
if created:
UserProfile.objects.create(user=instance)

# 3. Profile update view & form
# 4. Show profile on posts/comments
```

---

### 3.2 - Permissions & Groups (4 hours)

**Built-in Permissions:**
```python
# Django auto-creates: add, change, delete, view permissions per model

# Check permissions in view
from django.contrib.auth.decorators import permission_required

@permission_required('tasks.add_task')
def task_create(request):
pass

@permission_required('tasks.change_task', raise_exception=True)
def task_update(request, task_id):
pass

# Multiple permissions
from django.contrib.auth.decorators import user_passes_test

def is_staff_or_owner(user, task):
return user.is_staff or task.assigned_to == user

@user_passes_test(lambda u: u.is_staff)
def admin_dashboard(request):
pass
```

**Custom Permissions:**
```python
# tasks/models.py
class Task(models.Model):
# ... fields

class Meta:
permissions = [
('can_assign_task', 'Can assign task to users'),
('can_set_priority', 'Can set task priority'),
('can_view_all_tasks', 'Can view all tasks'),
]

# Usage in view
@permission_required('tasks.can_assign_task')
def assign_task(request, task_id):
pass

# In template
{% if perms.tasks.can_assign_task %}
<a href="{% url 'tasks:assign' task.id %}">Assign</a>
{% endif %}
```

**Groups:**
```python
# Create groups in shell or admin
from django.contrib.auth.models import Group, Permission

# Create groups
managers = Group.objects.create(name='Managers')
developers = Group.objects.create(name='Developers')

# Assign permissions
can_assign = Permission.objects.get(codename='can_assign_task')
can_set_priority = Permission.objects.get(codename='can_set_priority')

managers.permissions.add(can_assign, can_set_priority)

# Add user to group
user.groups.add(managers)

# Check in view
if request.user.groups.filter(name='Managers').exists():
# Manager-specific logic
pass
```

**Object-Level Permissions:**
```python
# Manual implementation
def task_update(request, task_id):
task = get_object_or_404(Task, id=task_id)

# Check ownership or staff
if task.assigned_to != request.user and not request.user.is_staff:
raise PermissionDenied

# ... update logic

# Or use django-guardian for per-object permissions
# pip install django-guardian
```

**Mixin-based Permissions (Class-Based Views):**
```python
from django.contrib.auth.mixins import LoginRequiredMixin, PermissionRequiredMixin
from django.views.generic import ListView, CreateView

class TaskListView(LoginRequiredMixin, ListView):
model = Task
template_name = 'tasks/task_list.html'
context_object_name = 'tasks'

class TaskCreateView(LoginRequiredMixin, PermissionRequiredMixin, CreateView):
model = Task
permission_required = 'tasks.add_task'
# ...
```

**Exercise 3.2:**
```python
# Blog Permissions System

# 1. Custom permissions:
#  - can_publish_post
#  - can_moderate_comments
#  - can_feature_post

# 2. Groups:
#  - Authors: can create/edit own posts
#  - Editors: can publish any post
#  - Moderators: can approve/delete comments

# 3. Implement:
#  - Authors can only edit their own posts
#  - Editors can publish anything
#  - Moderators can view/delete any comment
#  - Regular users can only read and comment

# 4. View protection:
#  - @permission_required decorators
#  - Object-level checks (owner vs editor)
```

---

## Module 4: Django REST Framework (12 hours)

### 4.1 - DRF Basics (4 hours)

**Setup:**
```bash
pip install djangorestframework
```

```python
# settings.py
INSTALLED_APPS = [
# ... django apps
'rest_framework',
'tasks',
]

REST_FRAMEWORK = {
'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
'PAGE_SIZE': 10
}
```

**Serializers:**
```python
# tasks/serializers.py
from rest_framework import serializers
from .models import Task
from django.contrib.auth.models import User

class UserSerializer(serializers.ModelSerializer):
class Meta:
model = User
fields = ['id', 'username', 'email']

class TaskSerializer(serializers.ModelSerializer):
assigned_to = UserSerializer(read_only=True)
assigned_to_id = serializers.IntegerField(write_only=True, required=False)

class Meta:
model = Task
fields = [
'id', 'title', 'description', 'status', 'priority',
'assigned_to', 'assigned_to_id',
'created_at', 'updated_at'
]
read_only_fields = ['created_at', 'updated_at']

def validate_title(self, value):
if len(value) < 5:
raise serializers.ValidationError('Title too short')
return value
```

**API Views:**
```python
# tasks/views.py
from rest_framework import generics, status
from rest_framework.decorators import api_view
from rest_framework.response import Response
from .models import Task
from .serializers import TaskSerializer

# Function-based views
@api_view(['GET', 'POST'])
def task_list(request):
if request.method == 'GET':
tasks = Task.objects.all()
serializer = TaskSerializer(tasks, many=True)
return Response(serializer.data)

elif request.method == 'POST':
serializer = TaskSerializer(data=request.data)
if serializer.is_valid():
serializer.save()
return Response(serializer.data, status=status.HTTP_201_CREATED)
return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

@api_view(['GET', 'PUT', 'DELETE'])
def task_detail(request, pk):
try:
task = Task.objects.get(pk=pk)
except Task.DoesNotExist:
return Response(status=status.HTTP_404_NOT_FOUND)

if request.method == 'GET':
serializer = TaskSerializer(task)
return Response(serializer.data)

elif request.method == 'PUT':
serializer = TaskSerializer(task, data=request.data)
if serializer.is_valid():
serializer.save()
return Response(serializer.data)
return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

elif request.method == 'DELETE':
task.delete()
return Response(status=status.HTTP_204_NO_CONTENT)

# Class-based views (preferred)
class TaskListCreate(generics.ListCreateAPIView):
queryset = Task.objects.all()
serializer_class = TaskSerializer

class TaskRetrieveUpdateDestroy(generics.RetrieveUpdateDestroyAPIView):
queryset = Task.objects.all()
serializer_class = TaskSerializer
```

**URLs:**
```python
# tasks/urls.py
from django.urls import path
from . import views

urlpatterns = [
# Function-based
path('api/tasks/', views.task_list),
path('api/tasks/<int:pk>/', views.task_detail),

# Or class-based
path('api/tasks/', views.TaskListCreate.as_view()),
path('api/tasks/<int:pk>/', views.TaskRetrieveUpdateDestroy.as_view()),
]
```

**Testing the API:**
```bash
# GET all tasks
curl http://localhost:8000/api/tasks/

# Create task
curl -X POST http://localhost:8000/api/tasks/ \
-H "Content-Type: application/json" \
-d '{"title": "New Task", "status": "TODO"}'

# Update task
curl -X PUT http://localhost:8000/api/tasks/1/ \
-H "Content-Type: application/json" \
-d '{"title": "Updated", "status": "DONE"}'

# Delete task
curl -X DELETE http://localhost:8000/api/tasks/1/
```

**Exercise 4.1:**
```python
# Blog REST API

# Serializers:
# - PostSerializer (nested author, category)
# - PostListSerializer (simpler, for list view)
# - CommentSerializer
# - CategorySerializer

# Views:
# - PostListCreate
# - PostRetrieveUpdateDestroy
# - CommentListCreate (for specific post)
# - CategoryList

# URLs:
# - /api/posts/
# - /api/posts/<slug>/
# - /api/posts/<slug>/comments/
# - /api/categories/
```

---

### 4.2 - ViewSets & Routers (3 hours)

**ViewSets:**
```python
# tasks/views.py
from rest_framework import viewsets
from rest_framework.decorators import action
from rest_framework.response import Response

class TaskViewSet(viewsets.ModelViewSet):
queryset = Task.objects.all()
serializer_class = TaskSerializer

# Custom action
@action(detail=False, methods=['get'])
def completed(self, request):
completed_tasks = Task.objects.filter(status='DONE')
serializer = self.get_serializer(completed_tasks, many=True)
return Response(serializer.data)

@action(detail=True, methods=['post'])
def complete(self, request, pk=None):
task = self.get_object()
task.status = 'DONE'
task.save()
serializer = self.get_serializer(task)
return Response(serializer.data)

# Override queryset for filtering
def get_queryset(self):
queryset = Task.objects.all()
status = self.request.query_params.get('status')
if status:
queryset = queryset.filter(status=status)
return queryset
```

**Routers:**
```python
# tasks/urls.py
from rest_framework.routers import DefaultRouter
from . import views

router = DefaultRouter()
router.register(r'tasks', views.TaskViewSet)

urlpatterns = router.urls

# Generates:
# GET  /tasks/         - list
# POST  /tasks/         - create
# GET  /tasks/<pk>/       - retrieve
# PUT  /tasks/<pk>/       - update
# PATCH /tasks/<pk>/       - partial update
# DELETE /tasks/<pk>/       - destroy
# GET  /tasks/completed/    - custom action
# POST  /tasks/<pk>/complete/  - custom detail action
```

**Nested Routers:**
```python
# pip install drf-nested-routers

from rest_framework_nested import routers

router = routers.DefaultRouter()
router.register(r'projects', ProjectViewSet)

project_router = routers.NestedDefaultRouter(router, r'projects', lookup='project')
project_router.register(r'tasks', TaskViewSet, basename='project-tasks')

urlpatterns = [
path('api/', include(router.urls)),
path('api/', include(project_router.urls)),
]

# Generates:
# /api/projects/
# /api/projects/<project_pk>/tasks/
# /api/projects/<project_pk>/tasks/<pk>/
```

**Exercise 4.2:**
```python
# Blog API with ViewSets

# ViewSets:
# - PostViewSet
#  - Custom action: @action(detail=False) def published(self, request)
#  - Custom action: @action(detail=True) def publish(self, request, pk)
# - CommentViewSet (nested under posts)

# Routers:
# - DefaultRouter for posts, categories
# - NestedRouter for /posts/<id>/comments/

# Filters:
# - Posts by category: /posts/?category=<id>
# - Posts by tag: /posts/?tag=<name>
# - Published only: /posts/?published=true
```

---

### 4.3 - Authentication & Permissions (3 hours)

**Token Authentication:**
```python
# settings.py
INSTALLED_APPS = [
# ...
'rest_framework.authtoken',
]

REST_FRAMEWORK = {
'DEFAULT_AUTHENTICATION_CLASSES': [
'rest_framework.authentication.TokenAuthentication',
'rest_framework.authentication.SessionAuthentication',
],
'DEFAULT_PERMISSION_CLASSES': [
'rest_framework.permissions.IsAuthenticatedOrReadOnly',
],
}

# Generate tokens
python manage.py migrate # Creates token table

# In shell:
from rest_framework.authtoken.models import Token
from django.contrib.auth.models import User
user = User.objects.get(username='demo_user')
token, created = Token.objects.get_or_create(user=user)
print(token.key)
```

**Login endpoint:**
```python
# accounts/views.py
from rest_framework.authtoken.models import Token
from rest_framework.decorators import api_view, permission_classes
from rest_framework.permissions import AllowAny
from rest_framework.response import Response
from django.contrib.auth import authenticate

@api_view(['POST'])
@permission_classes([AllowAny])
def login(request):
username = request.data.get('username')
password = request.data.get('password')

user = authenticate(username=username, password=password)
if user:
token, _ = Token.objects.get_or_create(user=user)
return Response({'token': token.key})

return Response({'error': 'Invalid credentials'}, status=400)

# Usage:
# curl -X POST http://localhost:8000/api/login/ \
#  -d "username=demo_user&password=mypass"
# Response: {"token": "abc123..."}

# Then use token:
# curl http://localhost:8000/api/tasks/ \
#  -H "Authorization: Token abc123..."
```

**Permissions:**
```python
# tasks/permissions.py
from rest_framework import permissions

class IsOwnerOrReadOnly(permissions.BasePermission):
def has_object_permission(self, request, view, obj):
# Read permissions allowed for any request
if request.method in permissions.SAFE_METHODS:
return True

# Write permissions only for owner
return obj.assigned_to == request.user

class IsStaffOrReadOnly(permissions.BasePermission):
def has_permission(self, request, view):
if request.method in permissions.SAFE_METHODS:
return True
return request.user.is_staff

# Usage in views
from .permissions import IsOwnerOrReadOnly

class TaskViewSet(viewsets.ModelViewSet):
queryset = Task.objects.all()
serializer_class = TaskSerializer
permission_classes = [permissions.IsAuthenticated, IsOwnerOrReadOnly]

def perform_create(self, serializer):
# Auto-assign to current user
serializer.save(assigned_to=self.request.user)
```

**JWT Authentication (better for SPAs):**
```python
# pip install djangorestframework-simplejwt

# settings.py
REST_FRAMEWORK = {
'DEFAULT_AUTHENTICATION_CLASSES': [
'rest_framework_simplejwt.authentication.JWTAuthentication',
],
}

# urls.py
from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView

urlpatterns = [
path('api/token/', TokenObtainPairView.as_view(), name='token_obtain_pair'),
path('api/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),
]

# Usage:
# curl -X POST http://localhost:8000/api/token/ \
#  -d "username=demo_user&password=mypass"
# Response: {"access": "...", "refresh": "..."}

# Use access token:
# curl http://localhost:8000/api/tasks/ \
#  -H "Authorization: Bearer <access_token>"
```

**Exercise 4.3:**
```python
# Blog API Authentication

# 1. JWT Authentication setup
# 2. Permissions:
#  - IsAuthorOrReadOnly (for posts)
#  - IsModeratorOrReadOnly (for comments)
# 3. Views:
#  - Anyone can read published posts
#  - Authors can create/edit own posts
#  - Editors can publish any post
#  - Moderators can delete comments
# 4. Test with curl/Postman
```

---

### 4.4 - Filtering, Searching, Ordering (2 hours)

**DRF Filters:**
```bash
pip install django-filter
```

```python
# settings.py
INSTALLED_APPS = [
# ...
'django_filters',
]

REST_FRAMEWORK = {
'DEFAULT_FILTER_BACKENDS': [
'django_filters.rest_framework.DjangoFilterBackend',
'rest_framework.filters.SearchFilter',
'rest_framework.filters.OrderingFilter',
],
}
```

**Filter Configuration:**
```python
# tasks/filters.py
from django_filters import rest_framework as filters
from .models import Task

class TaskFilter(filters.FilterSet):
title = filters.CharFilter(lookup_expr='icontains')
created_after = filters.DateTimeFilter(field_name='created_at', lookup_expr='gte')
created_before = filters.DateTimeFilter(field_name='created_at', lookup_expr='lte')
priority_min = filters.NumberFilter(field_name='priority', lookup_expr='gte')

class Meta:
model = Task
fields = {
'status': ['exact'],
'priority': ['exact', 'gte', 'lte'],
'assigned_to': ['exact'],
}

# tasks/views.py
from .filters import TaskFilter

class TaskViewSet(viewsets.ModelViewSet):
queryset = Task.objects.all()
serializer_class = TaskSerializer
filterset_class = TaskFilter
search_fields = ['title', 'description']
ordering_fields = ['created_at', 'priority', 'status']
ordering = ['-created_at']

# Usage:
# /api/tasks/?status=TODO
# /api/tasks/?priority__gte=2
# /api/tasks/?search=django
# /api/tasks/?ordering=-priority
# /api/tasks/?created_after=2026-01-01&created_before=2026-12-31
```

**Exercise 4.4:**
```python
# Blog API Filtering

# Filters:
# - published (boolean)
# - category (exact)
# - tags (in)
# - author (exact)
# - published_date__gte, published_date__lte
# - search (title, content)
# - ordering (created_at, published_date, title)

# Example queries:
# /api/posts/?published=true&category=1
# /api/posts/?tags=django,python
# /api/posts/?search=tutorial
# /api/posts/?ordering=-published_date
```

---

## Module 5: Advanced Django (12 hours)

### 5.1 - Signals & Middleware (3 hours)

**Signals:**
```python
# tasks/signals.py
from django.db.models.signals import post_save, pre_delete
from django.dispatch import receiver
from django.contrib.auth.models import User
from .models import Task

@receiver(post_save, sender=Task)
def task_created(sender, instance, created, **kwargs):
if created:
print(f'New task created: {instance.title}')
# Send notification, create audit log, etc.

@receiver(post_save, sender=Task)
def task_status_changed(sender, instance, created, **kwargs):
if not created and instance.status == 'DONE':
print(f'Task completed: {instance.title}')
# Send completion notification

@receiver(pre_delete, sender=Task)
def task_deleted(sender, instance, **kwargs):
print(f'Task being deleted: {instance.title}')
# Archive, log, etc.

# tasks/apps.py
from django.apps import AppConfig

class TasksConfig(AppConfig):
default_auto_field = 'django.db.models.BigAutoField'
name = 'tasks'

def ready(self):
import tasks.signals # Register signals
```

**Custom Signals:**
```python
# tasks/signals.py
from django.dispatch import Signal

task_assigned = Signal() # Custom signal

# Emit signal
from .signals import task_assigned

def assign_task(task, user):
task.assigned_to = user
task.save()
task_assigned.send(sender=Task, task=task, user=user)

# Listen to signal
from django.dispatch import receiver
from .signals import task_assigned

@receiver(task_assigned)
def on_task_assigned(sender, task, user, **kwargs):
# Send email notification
print(f'Task {task.title} assigned to {user.username}')
```

**Middleware:**
```python
# myproject/middleware.py
import time
import logging

logger = logging.getLogger(__name__)

class RequestLoggingMiddleware:
def __init__(self, get_response):
self.get_response = get_response

def __call__(self, request):
# Before view
start_time = time.time()
request_id = request.headers.get('X-Request-ID', 'no-id')

logger.info(f'Request started: {request.method} {request.path}', extra={
'request_id': request_id,
'user': request.user.username if request.user.is_authenticated else 'anonymous'
})

response = self.get_response(request)

# After view
duration = time.time() - start_time
logger.info(f'Request completed: {response.status_code}', extra={
'request_id': request_id,
'duration': f'{duration:.2f}s'
})

response['X-Request-ID'] = request_id
return response

# settings.py
MIDDLEWARE = [
# ... default middleware
'myproject.middleware.RequestLoggingMiddleware',
]
```

**Exercise 5.1:**
```python
# Signals & Middleware exercise

# 1. Signals:
#  - User registered → create UserProfile
#  - Post published → send notification to followers
#  - Comment created → notify post author

# 2. Custom signal: post_viewed
#  - Emit when post viewed
#  - Track view count

# 3. Middleware:
#  - Request timing
#  - User activity logging
#  - Rate limiting (simple IP-based)
```

---

### 5.2 - Caching (3 hours)

**Cache Configuration:**
```python
# settings.py
CACHES = {
'default': {
'BACKEND': 'django.core.cache.backends.redis.RedisCache',
'LOCATION': 'redis://127.0.0.1:6379/1',
}
}

# Or for development:
CACHES = {
'default': {
'BACKEND': 'django.core.cache.backends.locmem.LocMemCache',
}
}
```

**View Caching:**
```python
from django.views.decorators.cache import cache_page

@cache_page(60 * 15) # Cache for 15 minutes
def task_list(request):
tasks = Task.objects.all()
return render(request, 'tasks/task_list.html', {'tasks': tasks})
```

**Template Fragment Caching:**
```html
{% load cache %}

{% cache 500 sidebar request.user.username %}
<div class="sidebar">
{% for task in user_tasks %}
<p>{{ task.title }}</p>
{% endfor %}
</div>
{% endcache %}
```

**Low-Level Cache API:**
```python
from django.core.cache import cache

# Set
cache.set('my_key', 'value', timeout=300) # 5 minutes

# Get
value = cache.get('my_key')
value = cache.get('my_key', 'default_value')

# Delete
cache.delete('my_key')

# Increment
cache.set('counter', 0)
cache.incr('counter') # Now 1

# Get many
cache.set_many({'a': 1, 'b': 2, 'c': 3})
values = cache.get_many(['a', 'b', 'c'])

# Custom view with caching
def expensive_view(request):
result = cache.get('expensive_result')

if result is None:
# Expensive computation
result = perform_expensive_operation()
cache.set('expensive_result', result, 3600)

return render(request, 'result.html', {'result': result})
```

**Cache Invalidation:**
```python
from django.db.models.signals import post_save
from django.core.cache import cache

@receiver(post_save, sender=Task)
def invalidate_task_cache(sender, instance, **kwargs):
cache.delete('task_list')
cache.delete(f'task_{instance.id}')
```

**Exercise 5.2:**
```python
# Caching Strategy

# 1. Cache expensive queries:
#  - Most viewed posts (update hourly)
#  - Category post counts
#  - Tag cloud

# 2. Cache template fragments:
#  - Sidebar (user-specific)
#  - Recent comments
#  - Popular posts widget

# 3. Invalidation:
#  - Clear post cache when published
#  - Clear category cache when post added

# 4. Custom caching decorator:
#  @cache_user_specific(timeout=600)
```

---

### 5.3 - Async Views (Celery) (3 hours)

**Celery Setup:**
```bash
pip install celery redis
```

```python
# myproject/celery.py
import os
from celery import Celery

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'myproject.settings')

app = Celery('myproject')
app.config_from_object('django.conf:settings', namespace='CELERY')
app.autodiscover_tasks()

# myproject/__init__.py
from .celery import app as celery_app

__all__ = ('celery_app',)

# settings.py
CELERY_BROKER_URL = 'redis://localhost:6379/0'
CELERY_RESULT_BACKEND = 'redis://localhost:6379/0'
CELERY_ACCEPT_CONTENT = ['json']
CELERY_TASK_SERIALIZER = 'json'
```

**Tasks:**
```python
# tasks/tasks.py
from celery import shared_task
from django.core.mail import send_mail
from .models import Task
import time

@shared_task
def send_task_notification(task_id):
task = Task.objects.get(id=task_id)
send_mail(
f'Task Assigned: {task.title}',
f'You have been assigned: {task.title}',
'from@example.com',
[task.assigned_to.email],
)
return f'Email sent for task {task_id}'

@shared_task
def generate_report(user_id):
# Simulate expensive operation
time.sleep(10)

from django.contrib.auth.models import User
user = User.objects.get(id=user_id)
tasks = Task.objects.filter(assigned_to=user)

report = {
'total': tasks.count(),
'completed': tasks.filter(status='DONE').count(),
}

return report
```

**Using Tasks in Views:**
```python
# tasks/views.py
from .tasks import send_task_notification, generate_report

def assign_task(request, task_id):
task = get_object_or_404(Task, id=task_id)
user_id = request.POST.get('user_id')

task.assigned_to_id = user_id
task.save()

# Send email asynchronously
send_task_notification.delay(task.id)

return redirect('tasks:task_detail', task_id=task.id)

def request_report(request):
# Start background task
task = generate_report.delay(request.user.id)

return JsonResponse({
'task_id': task.id,
'status': 'processing'
})

def report_status(request, task_id):
from celery.result import AsyncResult
task = AsyncResult(task_id)

return JsonResponse({
'state': task.state,
'result': task.result if task.ready() else None
})
```

**Periodic Tasks:**
```python
# pip install django-celery-beat

# settings.py
INSTALLED_APPS = [
# ...
'django_celery_beat',
]

# Migrate
python manage.py migrate django_celery_beat

# myproject/celery.py
from celery.schedules import crontab

app.conf.beat_schedule = {
'send-daily-summary': {
'task': 'tasks.tasks.send_daily_summary',
'schedule': crontab(hour=9, minute=0), # 9am daily
},
'cleanup-old-tasks': {
'task': 'tasks.tasks.cleanup_old_tasks',
'schedule': crontab(hour=2, minute=0, day_of_week=1), # Monday 2am
},
}

# tasks/tasks.py
@shared_task
def send_daily_summary():
# Send summary to all users
pass

@shared_task
def cleanup_old_tasks():
from datetime import timedelta
from django.utils import timezone

cutoff = timezone.now() - timedelta(days=90)
Task.objects.filter(status='DONE', updated_at__lt=cutoff).delete()
```

**Run Celery:**
```bash
# Worker
celery -A myproject worker --loglevel=info

# Beat (scheduler)
celery -A myproject beat --loglevel=info

# Both
celery -A myproject worker --beat --loglevel=info
```

**Exercise 5.3:**
```python
# Blog Async Tasks

# Tasks:
# 1. send_post_notification - notify followers when post published
# 2. generate_sitemap - weekly sitemap generation
# 3. backup_database - daily database backup
# 4. moderate_comments - auto-check comments for spam (ML simulation)

# Periodic:
# - Daily: inactive user cleanup
# - Weekly: popular posts digest email
# - Monthly: analytics report

# Views:
# - Publish post → trigger notification task
# - Export data → async generation with status check
```

---

### 5.4 - Testing (3 hours)

**Test Setup:**
```python
# tasks/tests.py
from django.test import TestCase, Client
from django.contrib.auth.models import User
from .models import Task

class TaskModelTest(TestCase):
def setUp(self):
self.user = User.objects.create_user(
username='testuser',
password='testpass'
)
self.task = Task.objects.create(
title='Test Task',
description='Test Description',
assigned_to=self.user
)

def test_task_creation(self):
self.assertEqual(self.task.title, 'Test Task')
self.assertEqual(self.task.status, 'TODO')

def test_task_str(self):
self.assertEqual(str(self.task), 'Test Task')

def test_is_completed(self):
self.assertFalse(self.task.is_completed())
self.task.status = 'DONE'
self.assertTrue(self.task.is_completed())

class TaskViewTest(TestCase):
def setUp(self):
self.client = Client()
self.user = User.objects.create_user(
username='testuser',
password='testpass'
)
self.client.login(username='testuser', password='testpass')

def test_task_list(self):
response = self.client.get('/tasks/')
self.assertEqual(response.status_code, 200)
self.assertTemplateUsed(response, 'tasks/task_list.html')

def test_task_create(self):
response = self.client.post('/tasks/create/', {
'title': 'New Task',
'description': 'Description',
'status': 'TODO'
})
self.assertEqual(response.status_code, 302) # Redirect
self.assertTrue(Task.objects.filter(title='New Task').exists())

def test_task_create_requires_login(self):
self.client.logout()
response = self.client.get('/tasks/create/')
self.assertEqual(response.status_code, 302) # Redirect to login

class TaskAPITest(TestCase):
def setUp(self):
self.client = Client()
self.user = User.objects.create_user(
username='testuser',
password='testpass'
)

from rest_framework.authtoken.models import Token
self.token = Token.objects.create(user=self.user)

def test_api_task_list(self):
response = self.client.get('/api/tasks/',
HTTP_AUTHORIZATION=f'Token {self.token.key}'
)
self.assertEqual(response.status_code, 200)

def test_api_task_create(self):
response = self.client.post('/api/tasks/',
data={'title': 'API Task', 'status': 'TODO'},
content_type='application/json',
HTTP_AUTHORIZATION=f'Token {self.token.key}'
)
self.assertEqual(response.status_code, 201)

# Run tests
python manage.py test

# Specific app
python manage.py test tasks

# With coverage
pip install coverage
coverage run --source='.' manage.py test
coverage report
coverage html
```

**Exercise 5.4:**
```python
# Comprehensive Test Suite

# 1. Model tests:
#  - Post creation, validation
#  - Comment relationships
#  - Tag many-to-many

# 2. View tests:
#  - List, detail, create, update, delete
#  - Permission checks
#  - Form validation

# 3. API tests:
#  - CRUD operations
#  - Authentication
#  - Permissions
#  - Filtering

# 4. Integration tests:
#  - Full workflow: register → login → create post → publish

# Goal: >80% coverage
```

---

## Module 6: Deployment & Production (10 hours)

### 6.1 - Production Settings (2 hours)

**Environment-based Configuration:**
```python
# myproject/settings.py (split into multiple files)

# settings/base.py - Common settings
# settings/development.py - Dev-specific
# settings/production.py - Prod-specific

# settings/base.py
import os
from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent.parent.parent

SECRET_KEY = os.environ.get('SECRET_KEY')

INSTALLED_APPS = [
# ... apps
]

# settings/development.py
from .base import *

DEBUG = True
ALLOWED_HOSTS = ['localhost', '127.0.0.1']

DATABASES = {
'default': {
'ENGINE': 'django.db.backends.sqlite3',
'NAME': BASE_DIR / 'db.sqlite3',
}
}

# settings/production.py
from .base import *

DEBUG = False
ALLOWED_HOSTS = [os.environ.get('ALLOWED_HOST', 'example.com')]

DATABASES = {
'default': {
'ENGINE': 'django.db.backends.postgresql',
'NAME': os.environ.get('DB_NAME'),
'USER': os.environ.get('DB_USER'),
'PASSWORD': os.environ.get('DB_PASSWORD'),
'HOST': os.environ.get('DB_HOST'),
'PORT': os.environ.get('DB_PORT', '5432'),
}
}

SECURE_SSL_REDIRECT = True
SESSION_COOKIE_SECURE = True
CSRF_COOKIE_SECURE = True

# Use:
# export DJANGO_SETTINGS_MODULE=myproject.settings.production
# python manage.py runserver --settings=myproject.settings.development
```

**Static & Media Files:**
```python
# settings/base.py
STATIC_URL = '/static/'
STATIC_ROOT = BASE_DIR / 'staticfiles'

MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR / 'media'

# Collect static files
python manage.py collectstatic

# Serve in production (nginx/whitenoise)
pip install whitenoise

# settings/production.py
MIDDLEWARE = [
'django.middleware.security.SecurityMiddleware',
'whitenoise.middleware.WhiteNoiseMiddleware', # Add this
# ... other middleware
]

STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
```

---

### 6.2 - Docker & docker-compose (4 hours)

**Dockerfile:**
```dockerfile
# Dockerfile
FROM python:3.11-slim

ENV PYTHONUNBUFFERED=1
ENV DJANGO_SETTINGS_MODULE=myproject.settings.production

WORKDIR /app

# Install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy project
COPY . .

# Collect static files
RUN python manage.py collectstatic --noinput

# Run migrations and start server
CMD python manage.py migrate && \
gunicorn myproject.wsgi:application --bind 0.0.0.0:8000
```

**docker-compose.yml:**
```yaml
version: '3.8'

services:
db:
image: postgres:15
volumes:
- postgres_data:/var/lib/postgresql/data
environment:
POSTGRES_DB: django_db
POSTGRES_USER: django_user
POSTGRES_PASSWORD: django_password

redis:
image: redis:7-alpine

web:
build: .
command: gunicorn myproject.wsgi:application --bind 0.0.0.0:8000
volumes:
- .:/app
- static_volume:/app/staticfiles
- media_volume:/app/media
ports:
- "8000:8000"
env_file:
- .env
depends_on:
- db
- redis

celery:
build: .
command: celery -A myproject worker --loglevel=info
volumes:
- .:/app
env_file:
- .env
depends_on:
- db
- redis

celery-beat:
build: .
command: celery -A myproject beat --loglevel=info
volumes:
- .:/app
env_file:
- .env
depends_on:
- db
- redis

volumes:
postgres_data:
static_volume:
media_volume:
```

**.env file:**
```bash
SECRET_KEY=your-secret-key-here
DEBUG=False
ALLOWED_HOST=example.com

DB_NAME=django_db
DB_USER=django_user
DB_PASSWORD=django_password
DB_HOST=db
DB_PORT=5432

CELERY_BROKER_URL=redis://redis:6379/0
CELERY_RESULT_BACKEND=redis://redis:6379/0
```

**Run:**
```bash
docker-compose up --build
docker-compose run web python manage.py migrate
docker-compose run web python manage.py createsuperuser
```

---

### 6.3 - CI/CD (2 hours)

**GitHub Actions:**
```yaml
# .github/workflows/django-ci.yml
name: Django CI

on:
push:
branches: [ main ]
pull_request:
branches: [ main ]

jobs:
test:
runs-on: ubuntu-latest

services:
postgres:
image: postgres:15
env:
POSTGRES_PASSWORD: postgres
options: >-
--health-cmd pg_isready
--health-interval 10s
--health-timeout 5s
--health-retries 5

steps:
- uses: actions/checkout@v3

- name: Set up Python
uses: actions/setup-python@v4
with:
python-version: '3.11'

- name: Install dependencies
run: |
pip install -r requirements.txt

- name: Run tests
env:
DATABASE_URL: postgres://postgres:postgres@localhost/postgres
run: |
python manage.py test

- name: Check migrations
run: |
python manage.py makemigrations --check --dry-run
```

---

### 6.4 - Monitoring & Logging (2 hours)

**Logging Configuration:**
```python
# settings/production.py
LOGGING = {
'version': 1,
'disable_existing_loggers': False,
'formatters': {
'verbose': {
'format': '{levelname} {asctime} {module} {message}',
'style': '{',
},
},
'handlers': {
'file': {
'level': 'INFO',
'class': 'logging.handlers.RotatingFileHandler',
'filename': '/var/log/django/django.log',
'maxBytes': 1024 * 1024 * 10, # 10MB
'backupCount': 10,
'formatter': 'verbose',
},
'console': {
'class': 'logging.StreamHandler',
'formatter': 'verbose',
},
},
'root': {
'handlers': ['file', 'console'],
'level': 'INFO',
},
'loggers': {
'django': {
'handlers': ['file', 'console'],
'level': 'INFO',
'propagate': False,
},
},
}
```

**Health Checks:**
```python
# myproject/health.py
from django.http import JsonResponse
from django.db import connection

def health_check(request):
try:
# Database check
connection.ensure_connection()

return JsonResponse({
'status': 'healthy',
'database': 'ok',
})
except Exception as e:
return JsonResponse({
'status': 'unhealthy',
'error': str(e)
}, status=503)
```

---

## Useful Resources

**Official Django:**
- Django Docs: https://docs.djangoproject.com/
- DRF Docs: https://www.django-rest-framework.org/

**Tutorials:**
- Django Girls Tutorial
- Django for Beginners (William Vincent)
- Two Scoops of Django (best practices)

**Packages:**
- django-extensions - Development helpers
- django-debug-toolbar - Debug panel
- django-crispy-forms - Better form rendering
- django-allauth - Social authentication
- django-cors-headers - CORS handling

---

## Summary: Django vs Flask vs FastAPI

| Feature | Django | Flask | FastAPI |
|---------|--------|-------|---------|
| **Philosophy** | Batteries included | Micro, flexible | Modern, async-first |
| **Learning Curve** | Steep | Gentle | Moderate |
| **Admin Panel** | Built-in | None (ext: Flask-Admin) | None |
| **ORM** | Django ORM | SQLAlchemy (separate) | SQLAlchemy/Tortoise |
| **Forms** | Built-in | WTForms (separate) | Pydantic |
| **Auth** | Built-in | Flask-Login (separate) | OAuth2/JWT (manual) |
| **API** | DRF (separate package) | Flask-RESTful | Built-in (OpenAPI) |
| **Async Support** | Channels/ASGI | Gevent/Eventlet | Native async/await |
| **Speed** | Slower | Medium | Fastest |
| **Use Case** | Complex web apps | Small/medium APIs | Modern REST/GraphQL API |

**When Django:**
- Complex CRUD application
- Admin interface needed
- Rapid development (many built-in features)
- Content management

**When Flask:**
- Microservices
- Flexibility important
- Small/medium projects
- Simpler learning curve

**When FastAPI:**
- Modern REST API
- Async/await beneficial
- Auto-documentation needed (OpenAPI)
- Type safety (Pydantic)

---

**Last updated:** 2026-03-22
