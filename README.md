# Docker

### Prerequisites

A wish to suffer **OR** Grussmann as a teacher

### How to prepare for working with Docker

1. Accept it as a necessary evil
2. Lie to yourself that you'll *never do it again*

### What will you gain finishing this tutorial

Basic knowledge of Docker, Makefile and Python frameworks (Django and Flask)

### What will you lose finishing this tutorial

Will to live

### Installing Docker and docker-compose

1. To install docker type: **sudo apt install docker**

2. To install docker-compose type: **sudo apt install docker-compose**

### Starting Docker Daemon (if not running already)

1. Into command line type: **sudo dockerd**

### Granting Docker permissions

1. **sudo groupadd docker**
2. **sudo usermod -aG docker $USER**
3. **newgrp docker**
4. To verify you did it correctly: **docker run hello-world**

-----------------------------------

## How to use it

For almost all of the examples below (NOT DOCKER-COMPOSE / DJANGO) you'll have to build and run them

1. **docker build . -t my_image_name:latest**
2. **docker run -p 8000:80 my_image_name**

-----------------------------------

### Pure python Docker Image

1. Create Dockerfile and type into it:

        FROM python:latest
        ADD script.py /
        CMD ["python", "./script.py"]

2. **OR** the way we were taught:

        FROM ubuntu:20.04
        RUN apt update && apt -y upgrade
        RUN apt -y install python
        WORKDIR /app
        COPY . .
        CMD ["python", "./script.py"]

3. Create *script.py* file and type any code you want into it, for example, a simple program that outputs "Hello"

        print("Hello")

4. **Done**

-----------------------------------

### C++ with Makefile Docker Image

*Thanks to Vašek Pfleger for making it easy*
[vacexpfleger](www.github.com/vacexpfleger/makefile-gru)

1. Create Dockerfile and type into it:

        FROM ubuntu:20.04
        RUN apt update && apt -y upgrade
        RUN apt -y install make && apt -y install build-essential
        WORKDIR /app
        COPY . .
        RUN make
        CMD ./hello

2. Create Makefile and type into it (**spacing is important**):

        .PHONY: all
        all: hello

        hello: hello.cpp
            g++ -o hello *.cpp

        .PHONY: install
        install:
            mkdir -p binaries
            cp -p hello binaries

        .PHONY: clean
        clean:
            rm -f hello

3. Create *hello.cpp* file and type any code you want into it, for example, a simple program that outputs "Hello"

        #include <iostream>

        int main(){
            std::cout << "Hello world" << std::endl;
        }

4. **Done** You should be able to *(I haven't tested it yet)* include any other files like .h / .cpp

-----------------------------------

### Flask Docker Image

1. Create Dockerfile and type into it:

        FROM ubuntu:20.04
        RUN apt update && apt -y upgrade
        RUN apt-get install -y python python3-pip
        WORKDIR /app
        RUN pip install Flask
        COPY . /app
        ENTRYPOINT [ "python" ]
        CMD [ "app.py" ]

2. Create *app.py* file and type any code you want into it, for example, a simple "Hello World" Flask application

        from flask import Flask
        app = Flask(__name__)

        @app.route('/')
        def hello_world():
            return 'Hello World'


        if __name == '__main__':
            app.run(debug=True, host='0.0.0.0')

3. **Done**

-----------------------------------

### Django Docker Image

1. Clone a *django CMS Divio quickstart* repository: **git clone https://github.com/divio/django-cms-divio-quickstart**

2. Type: **docker-compose build**

3. Type: **docker-compose run web python manage.py migrate** and then **docker-compose run web python manage.py createsuperuser**

4. To create your "Hello world app first do: "**docker-compose run web django-admin.py startapp myapp**

5. You might experience permission problems so type: **sudo chmod -R 777 myapp**

6. Edit **views.py** inside the myapp folder to look like this:

        from django.shortcuts import render
        from django.http import HttpResponse


        def index(request):
            return HttpResponse("Hello world")

7. Create file named **urls.py** and add this bit of code:

        from django.urls import path

        from . import views

        urlpatterns = [
            path('', views.index, name='index'),
        ]

8. Go back to the root and then the quickstart folder, then also edit the **urls.py** to look like this:

        from django.conf import settings
        from django.conf.urls.static import static
        from django.contrib import admin
        from django.urls import path, include

        urlpatterns = [
            path('myapp/', include('myapp.urls')),
            path('admin/', admin.site.urls),
        ]

        if settings.DEBUG:
            urlpatterns.extend(static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT))

        urlpatterns.append(path('', include('cms.urls')))


9. You must then build your image with: **docker-compose build** and run it with **docker-compose up**

10. **Done**

-----------------------------------

# END

I want to kill myself.
