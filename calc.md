# Calculator Project

A web-based calculator built with Django that performs basic arithmetic operations (addition, subtraction, multiplication, division) through a user-friendly form interface.

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Project Structure](#project-structure)
- [Installation](#installation)
- [Usage](#usage)
- [File Details](#file-details)
- [API Endpoints](#api-endpoints)
- [Form Structure](#form-structure)

## 🎯 Overview

The Calculator Project is a Django web application that provides a simple calculator interface. Users can input two numbers, select an arithmetic operation, and get the result instantly. The application demonstrates Django forms, form processing, and basic arithmetic operations in a web environment.

## ✨ Features

- **Basic Arithmetic Operations**: Addition, subtraction, multiplication, division
- **Form-Based Interface**: Clean, user-friendly form for input
- **Error Handling**: Division by zero protection
- **Real-Time Results**: Immediate calculation display
- **Input Validation**: Form validation for numeric inputs
- **CSRF Protection**: Built-in security with CSRF tokens

## 📁 Project Structure

```
2.calculator_project/
├── calculator/                # Calculator app
│   ├── __init__.py
│   ├── admin.py              # Admin interface configuration
│   ├── apps.py               # App configuration
│   ├── forms.py              # Calculator form definition
│   ├── migrations/           # Database migrations
│   │   └── __init__.py
│   ├── models.py             # Database models (empty)
│   ├── tests.py              # Test cases
│   ├── urls.py               # App URL configuration
│   └── views.py              # Calculator logic
├── calculator_project/        # Main project directory
│   ├── __init__.py
│   ├── asgi.py               # ASGI configuration
│   ├── settings.py           # Django settings
│   ├── urls.py               # Main URL configuration
│   ├── views.py              # Project views (empty)
│   └── wsgi.py               # WSGI configuration
├── template/                 # HTML templates
│   └── calculator.html       # Calculator interface
├── db.sqlite3               # SQLite database
├── manage.py                # Django management script
└── README.md                # This file
```

## 🚀 Installation

### Prerequisites
- Python 3.7+
- Django 3.0+
- pip (Python package installer)

### Setup Instructions

1. **Navigate to the project directory:**
   ```bash
   cd 2.calculator_project
   ```

2. **Create a virtual environment:**
   ```bash
   python -m venv venv
   ```

3. **Activate the virtual environment:**
   ```bash
   # On Windows:
   venv\Scripts\activate
   
   # On macOS/Linux:
   source venv/bin/activate
   ```

4. **Install Django:**
   ```bash
   pip install django
   ```

5. **Run database migrations:**
   ```bash
   python manage.py migrate
   ```

6. **Start the development server:**
   ```bash
   python manage.py runserver
   ```

7. **Access the application:**
   - Calculator: http://127.0.0.1:8000/
   - Admin panel: http://127.0.0.1:8000/admin/

## 💻 Usage

### Using the Calculator
1. Open your browser and navigate to `http://127.0.0.1:8000/`
2. Enter the first number in the "First num" field
3. Select an operation from the dropdown menu:
   - `+` for addition
   - `-` for subtraction
   - `*` for multiplication
   - `/` for division
4. Enter the second number in the "Second num" field
5. Click "Calculate" to see the result
6. The result will appear below the form

### Supported Operations
- **Addition (+)**: Adds two numbers
- **Subtraction (-)**: Subtracts second number from first
- **Multiplication (*)**: Multiplies two numbers
- **Division (/)**: Divides first number by second (with zero protection)

## 📄 File Details

### Core Django Files

#### `manage.py`
- **Purpose**: Django's command-line utility for administrative tasks
- **Usage**: Run Django commands like `migrate`, `runserver`, `createsuperuser`

#### `calculator_project/settings.py`
- **Purpose**: Django project settings and configuration
- **Key Settings**: Database configuration, installed apps, middleware, templates

#### `calculator_project/urls.py`
```python
from django.contrib import admin
from django.urls import path,include
from . import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('',include('calculator.urls'))
]
```
- **Purpose**: Main URL routing configuration
- **Routes**:
  - `/admin/` → Django admin interface
  - `/` → Calculator app (includes calculator.urls)

#### `calculator_project/views.py`
- **Purpose**: Project-level views (currently empty)
- **Usage**: Can be used for project-wide view functions

### App Files

#### `calculator/forms.py`
```python
from django import forms

class cal_form(forms.Form):
    first_num = forms.IntegerField()
    operator_choices = [
        ('+','add'),
        ('-','sub'),
        ('*','mult'),
        ('/','div')
    ]
    operator = forms.ChoiceField(choices=operator_choices)
    second_num = forms.IntegerField()
```
- **Purpose**: Defines the calculator form structure
- **Fields**:
  - `first_num`: Integer field for first number
  - `operator`: Choice field with arithmetic operations
  - `second_num`: Integer field for second number
- **Validation**: Automatic integer validation for numeric inputs

#### `calculator/views.py`
```python
from django.shortcuts import render
from .forms import cal_form

def calculator_view(request):
    result = None
    if request.method == 'POST':
        form = cal_form(request.POST)
        if form.is_valid():
            num1 = form.cleaned_data['first_num']
            num2 = form.cleaned_data['second_num']
            opr = form.cleaned_data['operator']

            if opr == '+':
                result = num1 + num2
            elif opr == '-':
                result = num1 - num2
            elif opr == '*':
                result = num1 * num2
            elif opr == '/':
                if num2 != 0:
                    result = num1 / num2
                else:
                    result = "try using non zero number in the second number"
            else:
                result = 'error'
    else:
        form = cal_form()

    return render(request,'calculator.html',{'result':result,'form':form})
```
- **Purpose**: Handles calculator logic and form processing
- **Functions**:
  - `calculator_view(request)`: Main calculator view
- **Features**:
  - Form validation and processing
  - Arithmetic operation handling
  - Division by zero protection
  - Error handling

#### `calculator/urls.py`
```python
from django.urls import path
from . import views

urlpatterns = [
    path('',views.calculator_view)
]
```
- **Purpose**: App-level URL configuration
- **Routes**: `/` → calculator_view function

#### `calculator/models.py`
- **Purpose**: Database models (currently empty)
- **Usage**: No database models needed for this calculator

#### `calculator/admin.py`
- **Purpose**: Admin interface configuration
- **Usage**: No admin interface needed for this app

#### `calculator/apps.py`
- **Purpose**: App configuration for the calculator app
- **Usage**: Defines app metadata and configuration

### Template Files

#### `template/calculator.html`
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Calculator</title>
</head>
<body>
    <form method="POST">
        {% csrf_token %}
        {{ form.as_p }}
        <button type="submit">Calculate</button>

        <div>
            {% if result is not None %}
                <p>result: {{ result }}</p>
            {% endif %}
        </div>
    </form>
</body>
</html>
```
- **Purpose**: Calculator interface template
- **Features**:
  - Form rendering with Django template tags
  - CSRF token for security
  - Result display with conditional rendering
  - Clean, simple interface
- **Form Elements**:
  - First number input field
  - Operation selector dropdown
  - Second number input field
  - Calculate button
  - Result display area

## 🔗 API Endpoints

| URL Pattern | View Function | Description |
|-------------|---------------|-------------|
| `/` | `calculator_view` | Calculator interface and form processing |
| `/admin/` | Django Admin | Admin interface (if needed) |

## 📝 Form Structure

### Calculator Form Fields
| Field Name | Type | Description | Validation |
|------------|------|-------------|------------|
| `first_num` | IntegerField | First number for calculation | Must be a valid integer |
| `operator` | ChoiceField | Arithmetic operation | Must be one of: +, -, *, / |
| `second_num` | IntegerField | Second number for calculation | Must be a valid integer |

### Operator Choices
| Value | Label | Operation |
|-------|-------|-----------|
| `+` | add | Addition |
| `-` | sub | Subtraction |
| `*` | mult | Multiplication |
| `/` | div | Division |

## 🛠️ Development

### Adding New Features
1. **Add new operations** in `calculator/forms.py` (operator_choices)
2. **Update calculation logic** in `calculator/views.py`
3. **Enhance templates** in `template/calculator.html`
4. **Add URL patterns** if needed in `calculator/urls.py`

### Error Handling
The application includes error handling for:
- Division by zero
- Invalid form data
- Invalid operations

### Testing
- Run tests: `python manage.py test`
- The project includes a basic `tests.py` file for adding test cases

## 📝 Notes

- The application doesn't use a database (no models needed)
- All calculations are performed in real-time
- Form validation ensures proper numeric input
- CSRF protection is enabled for security
- The interface is simple and user-friendly

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## 📄 License

This project is open source and available under the MIT License.

---

**Happy Calculating! 🧮** 