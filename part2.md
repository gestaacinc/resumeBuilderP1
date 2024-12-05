### **Updated Part 2: Enhancing the Resume Builder**

In this phase, we will:
1. Enhance the design of the form using **Tailwind CSS** (via CDN for simplicity).
2. Use the `/` route as a **home page** to display saved resumes in a **card-based layout**.
3. Add a **menu** to navigate between building resumes and viewing saved resumes.
4. Create a Canva-style detailed resume page for viewing individual resumes.

---

## **Step 1: Install Tailwind CSS**

1. **Include Tailwind CSS via CDN**:
   Add the following `<link>` tag to the `<head>` section of `templates/base.html`:
   ```html
   <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet">
   ```

2. Update **`base.html`** for a modern layout:
   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>Resume Builder</title>
       <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet">
   </head>
   <body class="bg-gray-100">
       <header class="bg-blue-500 text-white py-4">
           <div class="container mx-auto flex justify-between items-center">
               <h1 class="text-2xl font-bold">Resume Builder</h1>
               <nav>
                   <a href="/" class="mx-2 hover:underline">View Resumes</a>
                   <a href="/add" class="mx-2 hover:underline">Build Resume</a>
               </nav>
           </div>
       </header>
       <main class="container mx-auto py-8">
           {% block content %}{% endblock %}
       </main>
   </body>
   </html>
   ```

---

## **Step 2: Update the `/` Route**

1. **Update `routes.py` to display resumes**:
   ```python
   from flask import render_template, request, redirect, url_for

   @app.route('/')
   def index():
       cur = mysql.connection.cursor()
       cur.execute("SELECT id, name FROM resumes")
       resumes = cur.fetchall()
       cur.close()
       return render_template('index.html', resumes=resumes)
   ```

2. **Create the `index.html` Template**:
   This template will display resumes in a **card-based layout**.
   ```html
   {% extends 'base.html' %}
   {% block content %}
       <h1 class="text-3xl font-bold mb-6">Saved Resumes</h1>
       <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-6">
           {% for resume in resumes %}
           <div class="bg-white shadow-md rounded-lg p-4 hover:shadow-lg transition-shadow">
               <h2 class="text-xl font-bold mb-2">{{ resume[1] }}</h2>
               <p class="text-gray-500 mb-4">Click below to view this resume.</p>
               <a href="/resume/{{ resume[0] }}" class="block bg-blue-500 hover:bg-blue-700 text-white text-center py-2 rounded-md transition">
                   View Resume
               </a>
           </div>
           {% endfor %}
       </div>
       {% if not resumes %}
       <p class="text-gray-600 text-center mt-4">No resumes found. Click "Build Resume" to get started!</p>
       {% endif %}
   {% endblock %}
   ```

---

## **Step 3: Enhance the Form**

1. **Improve `add_resume.html` with Tailwind CSS**:
   ```html
   {% extends 'base.html' %}
   {% block content %}
       <h1 class="text-3xl font-bold mb-6">Build a Resume</h1>
       <form method="post" class="bg-white shadow-md rounded px-8 pt-6 pb-8 mb-4">
           <div class="mb-4">
               <label for="name" class="block text-gray-700 font-bold mb-2">Name:</label>
               <input type="text" id="name" name="name" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" required>
           </div>
           <div class="mb-4">
               <label for="email" class="block text-gray-700 font-bold mb-2">Email:</label>
               <input type="email" id="email" name="email" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" required>
           </div>
           <div class="mb-4">
               <label for="phone" class="block text-gray-700 font-bold mb-2">Phone:</label>
               <input type="text" id="phone" name="phone" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" required>
           </div>
           <div class="mb-4">
               <label for="skills" class="block text-gray-700 font-bold mb-2">Skills:</label>
               <textarea id="skills" name="skills" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"></textarea>
           </div>
           <div class="mb-4">
               <label for="experience" class="block text-gray-700 font-bold mb-2">Experience:</label>
               <textarea id="experience" name="experience" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"></textarea>
           </div>
           <div class="mb-4">
               <label for="education" class="block text-gray-700 font-bold mb-2">Education:</label>
               <textarea id="education" name="education" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"></textarea>
           </div>
           <div class="flex items-center justify-between">
               <button type="submit" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline">
                   Save Resume
               </button>
           </div>
       </form>
   {% endblock %}
   ```

---

## **Step 4: Add Detailed Resume Page**

1. Add a new route in **`routes.py`**:
   ```python
   @app.route('/resume/<int:id>')
   def view_resume(id):
       cur = mysql.connection.cursor()
       cur.execute("SELECT name, email, phone, skills, experience, education FROM resumes WHERE id = %s", (id,))
       resume = cur.fetchone()
       cur.close()
       return render_template('view_resume.html', resume=resume)
   ```

2. Create **`view_resume.html`** with a Canva-inspired layout:
   ```html
   {% extends 'base.html' %}
   {% block content %}
   <div class="max-w-4xl mx-auto bg-white shadow-lg rounded-lg p-8">
       <div class="flex items-center border-b pb-6">
           <div class="w-32 h-32 bg-gray-200 rounded-full flex items-center justify-center text-gray-500 text-xl font-bold">
               Photo
           </div>
           <div class="ml-6">
               <h1 class="text-4xl font-bold text-gray-800">{{ resume[0] }}</h1>
               <p class="text-gray-600 text-lg">{{ resume[1] }}</p>
               <p class="text-gray-600 text-lg">{{ resume[2] }}</p>
           </div>
       </div>
       <div class="mt-8">
           <h2 class="text-2xl font-semibold text-gray-800 border-b pb-2 mb-4">Skills</h2>
           <ul class="list-disc list-inside text-gray-700">
               {% for skill in resume[3].split(',') %}
               <li>{{ skill.strip() }}</li>
               {% endfor %}
           </ul>
       </div>
       <div class="mt-8">
           <h2 class="text-2xl font-semibold text-gray-800 border-b pb-2 mb-4">Experience</h2>
           <p class="text-gray-700 whitespace-pre-line">{{ resume[4] }}</p>
       </div>
       <div class="mt-8">
           <h2 class="text-2xl font-semibold text-gray-800 border-b pb-2 mb-4">Education</h2>
           <p class="text-gray-700 whitespace-pre-line">{{ resume[5] }}</p>
       </div>
   </div>
   {% endblock %}
   ```

---

## **Step 5: Test the Application**

1. Start the Flask app:
   ```bash
   python run.py
   ```

2. Open the browser and navigate to:
   - **Home Page**: `http://127.0.0.1:5000/`
   - **Build Resume Page**:

 `http://127.0.0.1:5000/add`
   - **View Resume Page**: Click any card on the home page.

---

## **Expected Outcome**
- **Home Page**:
  - Resumes are displayed in a **card-based layout**.
  - Clicking "View Resume" opens the detailed resume page.

- **Detailed Resume Page**:
  - A Canva-inspired resume layout with sections for the photo, contact info, skills, experience, and education.

---

Let me know if you need further updates!
