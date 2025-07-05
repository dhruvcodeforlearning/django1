# Django Blog Project - Complete Code Documentation

## Project Structure
```
blog_proj/
├── blog_proj/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── blog_app/
│   ├── __init__.py
│   ├── admin.py
│   ├── forms.py
│   ├── models.py
│   ├── urls.py
│   └── views.py
├── templates/
│   ├── create_post.html
│   ├── post_detail.html
│   └── post_list.html
└── manage.py
```

---

## 1. Models (blog_app/models.py)

```python
from django.db import models

class post(models.Model):
    post_name = models.CharField(max_length=50)
    post_desc = models.TextField()
    post_time = models.DateTimeField(auto_now_add=True)
```

**Explanation:**
- `post_name`: CharField for post title (max 50 characters)
- `post_desc`: TextField for post content (unlimited length)
- `post_time`: DateTimeField that automatically sets current time when post is created

---

## 2. Forms (blog_app/forms.py)

```python
from django import forms

class create_post(forms.Form):
    p_name = forms.CharField(max_length=50, label='enter post name')
    p_desc = forms.CharField(label='Description', widget=forms.Textarea())
    p_time = forms.DateTimeField(label='DateTime', widget=forms.DateTimeInput())
```

**Explanation:**
- Form for creating new blog posts
- `p_name`: Text input for post title
- `p_desc`: Textarea for post description
- `p_time`: DateTime input for post timestamp

---

## 3. Views (blog_app/views.py)

```python
from django.shortcuts import render, redirect, get_object_or_404
from django.core.paginator import Paginator
from .models import post
from .forms import create_post

def createpost(request):
    if request.method == 'POST':
        form = create_post(request.POST)
        if form.is_valid():
            post.objects.create(
                post_name=form.cleaned_data['p_name'],
                post_desc=form.cleaned_data['p_desc'],
                post_time=form.cleaned_data['p_time']
            )
            return redirect('post_list')
    else:
        form = create_post()
    
    return render(request, 'create_post.html', {'form': form})

def post_list(request):
    posts = post.objects.all()
    paginator = Paginator(posts, 5)  # Show 5 posts per page
    page = request.GET.get('page')
    posts = paginator.get_page(page)
    return render(request, 'post_list.html', {'posts': posts})

def post_detail(request, id):
    post_obj = get_object_or_404(post, id=id)
    return render(request, 'post_detail.html', {'post': post_obj})
```

**Explanation:**
- `createpost`: Handles form submission and creates new posts
- `post_list`: Displays all posts with pagination (5 per page)
- `post_detail`: Shows individual post details

---

## 4. URLs (blog_app/urls.py)

```python
from django.urls import path
from blog_app import views

urlpatterns = [
    path('', views.post_list, name='post_list'),
    path('<int:id>/', views.post_detail, name='post_detail'),
    path('create/', views.createpost, name='create_post'),
]
```

**Explanation:**
- `''`: Home page showing post list
- `'<int:id>/'`: Individual post detail page
- `'create/'`: Create new post form

---

## 5. Admin (blog_app/admin.py)

```python
from django.contrib import admin
from .models import post

class postAdmin(admin.ModelAdmin):
    list_display = ('post_name', 'post_desc', 'post_time')

admin.site.register(post, postAdmin)
```

**Explanation:**
- Registers post model with Django admin
- Shows post name, description, and time in admin list view

---

## 6. Project URLs (blog_proj/urls.py)

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('blog_app.urls')),
]
```

**Explanation:**
- Includes admin URLs
- Includes blog app URLs at root level

---

## 7. Settings (blog_proj/settings.py) - Key Parts

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'blog_app',
]

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'templates'],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

---

## 8. Templates

### 8.1 Post List Template (templates/post_list.html)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Post List</title>
</head>
<body>
    <h2>Post List</h2>
    <a href="{% url 'create_post' %}">Create New Post</a>
    
    <ul>
        {% for post in posts %}
            <li><a href="{% url 'post_detail' post.id %}">{{ post.post_name }}</a></li>
        {% endfor %}
    </ul>

    <!-- Simple Pagination -->
    {% if posts.has_other_pages %}
        <div>
            {% if posts.has_previous %}
                <a href="?page={{ posts.previous_page_number }}">← Previous</a>
            {% endif %}
            
            <span>Page {{ posts.number }} of {{ posts.paginator.num_pages }}</span>
            
            {% if posts.has_next %}
                <a href="?page={{ posts.next_page_number }}">Next →</a>
            {% endif %}
        </div>
    {% endif %}
</body>
</html>
```

### 8.2 Post Detail Template (templates/post_detail.html)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Post Detail</title>
</head>
<body>
    <h2>{{ post.post_name }}</h2>
    <p>{{ post.post_desc }}</p>
    <p><em>{{ post.post_time }}</em></p>
    <a href="{% url 'post_list' %}">Back to Post List</a>
</body>
</html>
```

### 8.3 Create Post Template (templates/create_post.html)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Create Post</title>
</head>
<body>
    <h2>Create New Post</h2>
    <form method="post">
        {% csrf_token %}
        {{ form.as_p }}
        <button type="submit">Create Post</button>
    </form>
    <a href="{% url 'post_list' %}">Back to Post List</a>
</body>
</html>
```

---

## 9. Key Features

### ✅ **Core Functionality**
- Create new blog posts
- View list of all posts
- View individual post details
- Simple pagination (5 posts per page)

### ✅ **Admin Integration**
- Posts visible in Django admin panel
- Easy post management

### ✅ **URL Structure**
- `/` - Post list (home page)
- `/create/` - Create new post
- `/<id>/` - View specific post

### ✅ **Form Handling**
- Form validation
- Automatic redirect after successful creation
- CSRF protection

---

## 10. Setup Instructions

1. **Install Django:**
   ```bash
   pip install django
   ```

2. **Run Migrations:**
   ```bash
   python manage.py makemigrations
   python manage.py migrate
   ```

3. **Create Superuser:**
   ```bash
   python manage.py createsuperuser
   ```

4. **Run Development Server:**
   ```bash
   python manage.py runserver
   ```

5. **Access URLs:**
   - Home: `http://localhost:8000/`
   - Admin: `http://localhost:8000/admin/`
   - Create Post: `http://localhost:8000/create/`

---

## 11. Database Schema

| Field | Type | Description |
|-------|------|-------------|
| id | AutoField | Primary key (auto-generated) |
| post_name | CharField(50) | Post title |
| post_desc | TextField | Post content |
| post_time | DateTimeField | Creation timestamp |

---

*This documentation covers all the essential code for a simple Django blog with pagination, forms, and admin integration.* 