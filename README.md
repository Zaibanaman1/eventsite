# eventsite
events viewing site
#home.html // the index page
<!DOCTYPE html>
<html lang="en">
<head>
    {% load static %}
    <script src="https://code.jquery.com/jquery-3.6.0.slim.min.js" integrity="sha256-u7e5khyithlIdTpu22PHhENmPcRdFiHRjhAuHcs05RI=" crossorigin="anonymous"></script>
    <script type="text/javascript">
        var user = '{{request.user}}'
        function getToken(name) {
        let cookieValue = null;
        if (document.cookie && document.cookie !== '') {
            const cookies = document.cookie.split(';');
            for (let i = 0; i < cookies.length; i++) {
                const cookie = cookies[i].trim();
                // Does this cookie string begin with the name we want?
                if (cookie.substring(0, name.length + 1) === (name + '=')) {
                    cookieValue = decodeURIComponent(cookie.substring(name.length + 1));
                    break;
                }
            }
        }
        return cookieValue;
    }
  const csrftoken = getToken('csrftoken');
  
  function getCookie(name){
    var cookieArr = document.cookie.split(";");
  
    for(var i=0;i<cookieArr.length; i++){
      var cookiePair = cookieArr[i].split('=');
  
      if(name == cookiePair[0].trim()){
        return decodeURIComponent(cookiePair[1]);
  
      }
    }
  
    return null;
  }
  
  
  
      </script>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" type="text/css" href="{% static 'css/home.css' %}">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-BmbxuPwQa2lc/FVzBcNJ7UAyJxM6wuqIj61tLrc4wSX0szH/Ev+nYRRuWlolflfl" crossorigin="anonymous">
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta2/dist/js/bootstrap.bundle.min.js" integrity="sha384-b5kHyXgcpbZJO/tY9Ul7kGkf1S0CWuKcCD38l8YkeH8z8QjE0GmW1gYU5S9FOnJ0" crossorigin="anonymous"></script>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">
<title>Document</title>
</head>
<body>
    <nav class="navbar navbar-expand-lg navbar-light bg-light">
      <div style="display: flex;" > 
      <h3 class="logo" href="google.com" >Eventzone</h3> 
      <form style="display: flex;padding-left: 20px;padding-top: 10px;" action="POST">
          <input  class="searchbar" type="text" placeholder="search for events" name="search" ><button class="searchbutton fa fa-search"></button>
      </form>
      <h3 style="padding-left: 6cm;cursor: pointer; border: 1mm;position: relative;right:5cm;top: 0.5cm;width: fit-content;" onclick="create()" class="fa crtbtn fa-plus-circle" > Create event</h3>     
    </div>
      </nav>
      <div style="padding-left: 1cm;" id="createbox" class="createbox hidden" >
          <button style="position: relative;left: 29cm;background-color: transparent;border: thin;" class="fa fa-times" onclick="exit()"></button>
          <form enctype="multipart/form-data" method="POST" action="createEvent" name="createfrom" >
               {% csrf_token %}
              <label for="eventname">name</label>
              <input type="text" name="eventname" placeholder="enter the Event name">
              <label for="desc">Description</label>
              <input type="text" name="desc" placeholder="Event Description">
              <label for="loc">Location</label>
              <input type="text" name="loc" placeholder="Event Location">
              <label for="date">event date</label>
              <input type="datetime-local" name="date" placeholder="DD/YY/MM">
              <label style="position: relative;left: 3cm ;top: 1cm;left: -22cm;" for="picture">picture</label>
              <input style="position: relative;top: 0.2cm;right: -9cm;width: 8cm;" type="file" name="picture">
              <input style="position:relative;top: 0.2cm;right: -7cm;" type="submit" value="Create Event" onclick=formprocess() > 
          </form>

      </div>
   <div style=" width: fit-content;padding-right: 6cm;padding-top: 1.5cm;;" class="container">
       <h1>Likes</h1>
       {% for event in events %}
       <div style="padding-bottom: .5cm; border-bottom: 10px;;" class="box">
       <img id="eventimage" src="{{event.imageURL}}" alt="no image">
       <ul>
           <li style="font-weight: bold;"> {{event.name}}</li>
           <li style="font-size: x-small;">{{event.date}}</li>
           <li style="font-size: x-small;">{{event.data}}</li>
       </ul>
       <div style="display: flex;"  class="impressions">
        <p style="width: fit-content;padding-top: 2cm;position: relative;left: 2.5cm;" class="fa fa-dropbox"></p>
        {% if event.liked %}
    
        <button  style="color: red;"  data-id="{{event.id}}" data-flag="{{event.liked}}" class="heartbutton fa fa-heart" ></button>
        {% else %}   
        <button style="color:rgb(228, 226, 226)"   id="{{event.id}}" data-id="{{event.id}}" data-flag="{{event.flag}}" class="heartbutton fa fa-heart" ></button> 
        {% endif %}
    </div>
       </div>
       {% endfor %}

   </div> 
</body>
<script type="text/javascript"> 
 function create(){
     document.getElementById('createbox').classList.remove('hidden')
 }

 function exit(){
     document.getElementById('createbox').classList.add('hidden')
 }



 button = document.getElementsByClassName("heartbutton");
 for(var i=0; i<button.length;i++){
     button[i].addEventListener('click',function(){
         var eventId = this.dataset.id
         var flag = this.dataset.flag
         
         
         updateflag(flag,eventId)
     })
 }
 
 
function updateflag(flag,eventId){

 var url = '/updateflag/'
    fetch(url, {
        method :'POST',
        headers:{
            'Content-Type':'application/json',
            'X-CSRFToken':csrftoken,
        },
        body:JSON.stringify({'eventId':eventId,'flag':flag})
    })
    .then((response) =>{
        return response.json()
    })

    .then((data) =>{
        console.log('data:', data)
        var idd = eventId
        var flag = data
      
        location.reload()
       
    })
}


</script>
</html>

#views.py
from django.shortcuts import render, redirect
from .models import *
import json
from django.http import JsonResponse
from .forms import CreateForm


def home(request):
    form = CreateForm()
    events = event.objects.all()
    context = {'events': events, 'form': form}

    return render(request, 'myapp/home.html', context)


def updateflag(request):
    data = json.loads(request.body)
    eventId = data['eventId']
    flag = data['flag']
    Event = event.objects.get(id=eventId)
    flag1 = not flag
    Event.liked = flag1
    Event.save()
    print(flag1)
    return JsonResponse(Event.liked, safe=False)


def createEvent(request):
    if request.method == 'POST':
        name = request.POST['eventname']
        print(name)
        des = request.POST['desc']
        print(des)
        loc = request.POST['loc']
        print(loc)
        img = request.FILES['picture']
        Date = request.POST['date']

        Event = event.objects.get_or_create(
            name=name, image=img, data=des, date=Date, location=loc)
        print(Event)

    return redirect("/")
#models.py // database structure 
from django.db import models


class event(models.Model):
    name = models.CharField(max_length=128, null=True,)
    image = models.ImageField(null=True, blank=True)
    data = models.CharField(max_length=128, null=True,)
    liked = models.BooleanField(default=False)
    date = models.DateTimeField(null=True)
    location = models.CharField(max_length=128, null=True,)

    @property
    def imageURL(self):
        try:

            url = self.image.url
        except:
            url = ''
        return url
#settings.py // have used mySql 
"""
Django settings for event project.

Generated by 'django-admin startproject' using Django 3.1.7.

For more information on this file, see
https://docs.djangoproject.com/en/3.1/topics/settings/

For the full list of settings and their values, see
https://docs.djangoproject.com/en/3.1/ref/settings/
"""

from pathlib import Path
import os

# Build paths inside the project like this: BASE_DIR / 'subdir'.
BASE_DIR = Path(__file__).resolve().parent.parent


# Quick-start development settings - unsuitable for production
# See https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/

# SECURITY WARNING: keep the secret key used in production secret!
SECRET_KEY = 'x8_x%qmisebih1d5wg!g8@c3)zf$+_fy-xbmk!(cjw5wsz!+f)'

# SECURITY WARNING: don't run with debug turned on in production!
DEBUG = True

ALLOWED_HOSTS = []


# Application definition

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'myapp.apps.MyappConfig',
]

MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]

ROOT_URLCONF = 'event.urls'

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [],
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

WSGI_APPLICATION = 'event.wsgi.application'


# Database
# https://docs.djangoproject.com/en/3.1/ref/settings/#databases

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'eventsite',
        'HOST': '127.0.0.1',
        'PORT': '3306',
        'USER': 'root',
        'PASSWORD': '321456',
    }
}


# Password validation
# https://docs.djangoproject.com/en/3.1/ref/settings/#auth-password-validators

AUTH_PASSWORD_VALIDATORS = [
    {
        'NAME': 'django.contrib.auth.password_validation.UserAttributeSimilarityValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.MinimumLengthValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.CommonPasswordValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.NumericPasswordValidator',
    },
]


# Internationalization
# https://docs.djangoproject.com/en/3.1/topics/i18n/

LANGUAGE_CODE = 'en-us'

TIME_ZONE = 'UTC'

USE_I18N = True

USE_L10N = True

USE_TZ = True


# Static files (CSS, JavaScript, Images)
# https://docs.djangoproject.com/en/3.1/howto/static-files/

STATIC_URL = '/static/'

STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'static')
]


MEDIA_URL = '/images/'

MEDIA_ROOT = os.path.join(BASE_DIR, 'static/images/')
