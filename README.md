# Upload Django Ke Heroku
Konfigurasi untuk mengupload Django Ke Heroku

## Buat folder project
* mkdir nama_folder
* cd nama_folder

## Buat dan aktifkan virtualenv
* phython -m venv myVenv (windows)
* virtualenv -p python3 .vEnv
* . .myVenv\Script\activate (windows)
* . .vEnv/bin/activate

## Install django
* pip install django

## Buat django project
* django-admin startproject myproject

## Buat Git repository
* git init 
* Buat file bernama `.gitignore` dengan isi sbb:
```
# jika menggunakan pyCharm IDE
.idea
# jika menggunakan db sqlite3
*.sqlite3
# Nama virtual env
.vEnv
*pyc
```
* git add .
* git commit -m 'First commit'

## Menyembunyikan instance configuration
* pip install python-decouple
* buat file .env di folder utama dan masukkan variabel berikut
- SECRET_KEY=Your$eCretKeyHere (secrety key dari settings.py dipindah kesini dengan maksud keamanan)
- DEBUG=True
* jangan lupa masukkan .env didalam file .gitignore

### Settings.py
* from decouple import config
* SECRET_KEY = config('SECRET_KEY')
* DEBUG = config('DEBUG', default=False, cast=bool)

## Mengatur database
* pip install dj-database-url

### Settings.py
* from dj_database_url import parse as dburl

default_dburl = 'sqlite:///' + os.path.join(BASE_DIR, 'db.sqlite3')

DATABASES = {
    'default': config('DATABASE_URL', default=default_dburl, cast=dburl),
}


## Static files 
pip install dj-static

### wsgi
* from dj_static import Cling
* application = Cling(get_wsgi_application())

### Settings.py
* STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
* STATICFILES_DIRS = (
    os.path.join(BASE_DIR, 'static'),
)
## Whitenoise
* pip install whitenoise
* masukkan whitenoise ke requirement
* masukkan ke setting midleware  == 'whitenoise.middleware.WhiteNoiseMiddleware',

## Buat sebuah requirements-dev.txt
pip freeze > requirements-dev.txt

## Buat sebuah file requirements.txt dan masukkan referensi dari requirements-dev ditambah 2 kebutuhan lain
* -r requirements-dev.txt
* gunicorn
* psycopg2

## Membuat file Procfile di folder root dan tambahkan code berikut
* web: gunicorn namaapps.wsgi --log-file -

## Membuat file runtime.txt dan tambahkan core
* python-3.6.0

## Membuat app di heroku
Install dulu heroku cli di komputer ( http://bit.ly/2jCgJYW ) 
* heroku apps:create app-name
jangan lupa untuk mengambil alamat aplikasinya

## Setting allowed hosts
* masukkan alamat heroku di ALLOWED_HOSTS pada settings.py (django)- Hanya domainnya saja, (ALLOWED_HOSTS = ['meretas.herokuapp.com',])

## Install heroku config plugin
* heroku plugins:install heroku-config

### Kirimkan file konfigurasi .env ke Heroku ( kamu harus berada di dalam folder dimana file .env berada)
* heroku plugins:install heroku-config
* heroku app
* heroku config:push -app namaapps di heroku

### Untuk menampilkan heroku config yang sudah di upload
* heroku config 

## Publish app
* git add .
* git commit -m 'Configuring the app'
* git remote -v , untuk melihat remote
* git remote add heroku https://git.heroku.com/meretas.git
* heroku --buildpack heroku/python
* git push heroku master

## Membuat data base
* heroku run python manage.py migrate

## Membuat Django admin user
* heroku run python manage.py createsuperuser

## EXTRAS
### Disable collectstatic
* heroku config:set DISABLE_COLLECTSTATIC=1

### Merubah configurasi (DEBUG=False jika sudah tahap produksi)
* heroku config:set DEBUG=True
