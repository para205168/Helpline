# requirements.txt
Django>=4.2
psycopg2-binary
whitenoise
djangorestframework
django-cors-headers
gunicorn
python-decouple


# Procfile
web: gunicorn your_project_name.wsgi:application


# render_setup_instructions.md

## 🔧 Render Deployment Setup (for Django Backend)

### ✅ Prerequisites:
- Your Django project is pushed to GitHub
- You have a Render.com account

### 📦 Step 1: Prepare Django Project

1. **Install these packages:**
   ```bash
   pip install gunicorn whitenoise psycopg2-binary django-cors-headers python-decouple
   pip freeze > requirements.txt
   ```

2. **Add to `settings.py`**:
   ```python
   import os
   from decouple import config

   SECRET_KEY = config('SECRET_KEY')
   DEBUG = False
   ALLOWED_HOSTS = ['your-subdomain.onrender.com', 'localhost']

   MIDDLEWARE += ['whitenoise.middleware.WhiteNoiseMiddleware']
   STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
   STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
   ```

3. **Create `.env` file:**
   ```env
   SECRET_KEY=your_django_secret_key
   EMAIL_HOST_USER=your_email@gmail.com
   EMAIL_HOST_PASSWORD=your_app_password
   ```

4. **Procfile** (create in root directory):
   ```
   web: gunicorn your_project_name.wsgi:application
   ```

### 🚀 Step 2: Deploy on Render

1. Go to [https://render.com](https://render.com)
2. Click **New Web Service**
3. Connect your GitHub repo
4. Fill out settings:
   - Runtime: `Python`
   - Build Command:
     ```bash
     pip install -r requirements.txt && python manage.py migrate && python manage.py collectstatic --noinput
     ```
   - Start Command:
     ```bash
     gunicorn your_project_name.wsgi:application
     ```
5. Add Environment Variables from `.env`
6. Click **Create Web Service**

### ✅ Done!
Once deployed, you’ll get a URL like:
```
https://helpline-backend.onrender.com
```
Use this in your frontend API calls.
