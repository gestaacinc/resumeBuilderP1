# Resume Builder Web Application (Phase 1)

## Objective
Build the foundation for a Resume Builder web application using Flask and MySQL. This phase includes:
1. Creating the database schema.
2. Installing necessary requirements.
3. Building a form for adding resume data.

---

## Step 1: Create the Database Schema

1. Open **XAMPP Control Panel** and start:
   - **Apache**
   - **MySQL**

2. Open **phpMyAdmin** in your browser:
   ```
   http://localhost/phpmyadmin
   ```

3. Create a new database:
   - Name it `resume_builder`.

4. Run the following SQL to create the `resumes` table:
   ```sql
   CREATE TABLE resumes (
       id INT AUTO_INCREMENT PRIMARY KEY,
       name VARCHAR(255) NOT NULL,
       email VARCHAR(255) NOT NULL,
       phone VARCHAR(15) NOT NULL,
       skills TEXT,
       experience TEXT,
       education TEXT
   );
   ```

---

## Step 2: Install Necessary Requirements

1. Open **Command Prompt** and navigate to your project directory:
   ```bash
   mkdir resume_builder
   cd resume_builder
   ```

2. Create a virtual environment:
   ```bash
   python -m venv venv
   ```

3. Activate the virtual environment:
   - On Windows:
     ```bash
     venv\Scripts\activate
     ```

4. Install Flask and MySQL libraries:
   ```bash
   pip install flask flask-mysqldb
   ```

---

## Step 3: Create the Flask Project Structure

Organize your project files as follows:
```
resume_builder/
├── templates/
│   ├── base.html
│   ├── add_resume.html
├── app/
│   ├── __init__.py
│   ├── routes.py
├── run.py
└── config.py
```

---

## Step 4: Configure Flask

1. **`config.py`**:
   ```python
   MYSQL_HOST = 'localhost'
   MYSQL_USER = 'root'
   MYSQL_PASSWORD = ''
   MYSQL_DB = 'resume_builder'
   ```

2. **`__init__.py`**:
   ```python
   from flask import Flask
   from flask_mysqldb import MySQL

   app = Flask(__name__)
   app.config.from_object('config')
   mysql = MySQL(app)

   from app.routes import *
   ```

3. **`run.py`**:
   ```python
   from app import app

   if __name__ == "__main__":
       app.run(debug=True)
   ```

---

## Step 5: Create a Route for Adding Resumes

1. **`routes.py`**:
   ```python
   from app import app, mysql
   from flask import render_template, request, redirect, url_for

   @app.route('/add', methods=['GET', 'POST'])
   def add_resume():
       if request.method == 'POST':
           name = request.form['name']
           email = request.form['email']
           phone = request.form['phone']
           skills = request.form['skills']
           experience = request.form['experience']
           education = request.form['education']

           cur = mysql.connection.cursor()
           cur.execute("""
               INSERT INTO resumes (name, email, phone, skills, experience, education)
               VALUES (%s, %s, %s, %s, %s, %s)
           """, (name, email, phone, skills, experience, education))
           mysql.connection.commit()
           cur.close()

           return redirect(url_for('add_resume'))

       return render_template('add_resume.html')
   ```

---

## Step 6: Create the Form

1. **`templates/add_resume.html`**:
   ```html
   {% extends 'base.html' %}
   {% block content %}
       <h1>Add Resume</h1>
       <form method="post">
           <label for="name">Name:</label>
           <input type="text" id="name" name="name" required>

           <label for="email">Email:</label>
           <input type="email" id="email" name="email" required>

           <label for="phone">Phone:</label>
           <input type="text" id="phone" name="phone" required>

           <label for="skills">Skills:</label>
           <textarea id="skills" name="skills"></textarea>

           <label for="experience">Experience:</label>
           <textarea id="experience" name="experience"></textarea>

           <label for="education">Education:</label>
           <textarea id="education" name="education"></textarea>

           <button type="submit">Save Resume</button>
       </form>
   {% endblock %}
   ```

2. **`templates/base.html`**:
   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>Resume Builder</title>
   </head>
   <body>
       <header>
           <h1>Resume Builder</h1>
           <nav>
               <a href="/add">Add Resume</a>
           </nav>
       </header>
       <main>
           {% block content %}{% endblock %}
       </main>
   </body>
   </html>
   ```

---

## Step 7: Run the Application

1. Start the Flask app:
   ```bash
   python run.py
   ```

2. Open your browser and navigate to:
   ```
   http://127.0.0.1:5000/add
   ```

---

## Expected Outcome
You should see a form where you can input resume details. After submitting the form:
- The data will be saved to the `resumes` table in the MySQL database.
- The page will reload, ready for another input.

---
 
