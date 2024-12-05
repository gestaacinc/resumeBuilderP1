### **Part 2: Enhancing the Resume Builder**

In this phase, we will:
1. Enhance the design of the form using **Tailwind CSS** (via CDN for simplicity).
2. Use the `/` route as a **home page** to display a list of saved resumes.
3. Add a **menu** to navigate between building resumes and viewing saved resumes.

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
       cur.execute("SELECT id, name, email, phone FROM resumes")
       resumes = cur.fetchall()
       cur.close()
       return render_template('index.html', resumes=resumes)
   ```

2. **Create the `index.html` Template**:
   This template will display a list of saved resumes.
   ```html
   {% extends 'base.html' %}
   {% block content %}
       <h1 class="text-3xl font-bold mb-6">Saved Resumes</h1>
       {% if resumes %}
           <table class="table-auto w-full border-collapse border border-gray-300">
               <thead class="bg-gray-200">
                   <tr>
                       <th class="border border-gray-300 px-4 py-2">Name</th>
                       <th class="border border-gray-300 px-4 py-2">Email</th>
                       <th class="border border-gray-300 px-4 py-2">Phone</th>
                       <th class="border border-gray-300 px-4 py-2">Actions</th>
                   </tr>
               </thead>
               <tbody>
                   {% for resume in resumes %}
                       <tr>
                           <td class="border border-gray-300 px-4 py-2">{{ resume[1] }}</td>
                           <td class="border border-gray-300 px-4 py-2">{{ resume[2] }}</td>
                           <td class="border border-gray-300 px-4 py-2">{{ resume[3] }}</td>
                           <td class="border border-gray-300 px-4 py-2">
                               <a href="/resume/{{ resume[0] }}" class="text-blue-500 hover:underline">View</a>
                           </td>
                       </tr>
                   {% endfor %}
               </tbody>
           </table>
       {% else %}
           <p class="text-gray-600">No resumes found. Click "Build Resume" to get started!</p>
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

## **Step 4: Test the Application**

1. Start the Flask app:
   ```bash
   python run.py
   ```

2. Open your browser and navigate to:
   - **Home Page**: `http://127.0.0.1:5000/`
   - **Build Resume Page**: `http://127.0.0.1:5000/add`

---

## **Expected Outcome**
- The **home page (`/`)** now displays a list of saved resumes with a clean design.
- The **form page (`/add`)** uses Tailwind CSS for a modern, user-friendly design.
- Users can navigate between building and viewing resumes via the **menu** in the header.

---

Let me know when you're ready for the next phase, such as **viewing individual resumes in detail** or **adding edit/delete functionality**!
