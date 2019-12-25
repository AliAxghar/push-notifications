# push-notifications
# Django app for Firebase Cloud Messaging 
# Send push notifications to mobile devices (android / ios) & browsers (chrome / firefox / .... ) using CRONJOBS.

## FCMDevice model fields:
* registration_id (required)
* name (optional)
* active (default:True)
* user (optional)
* device_id (required)
* type ('android', 'web', 'ios')(required)

## Functionalities:
* all migrations
* model django admin
* sending single and bulk notification 
* sending notification to single and all devices
* Django rest framework viewsets

## implimentation:
Install library from pypi using [fcm-django](https://pypi.org/project/fcm-django/) and also install [cronjob](https://pypi.org/project/cronjob/)
```
pip install fcm-django
pip install cronjob
```
Add fcm_django and cron_tabs into your settings.py file 
```
INSTALLED_APPS = (

        "fcm_django"
        "django_crontab"
)
```
 ``` manage.py migrate ``` will install and migrate FCMDevice Model.
 
Also Add FCM_SERVER_KEY and CRONJOBS(when you want to run thread) into your project settings.py
```
FCM_DJANGO_SETTINGS = {
        "FCM_SERVER_KEY": "[your api key]",
        "ONE_DEVICE_PER_USER": True/False,
        "DELETE_INACTIVE_DEVICES": True/False,
}
CRONJOBS = [
    ('0 0 * * *', 'api.cron.run') 
    ]
    # api is my app name  
    # run is my method name define in code
    # The main part of a cron is its timing syntax which defines the schedule at which the job has to be run periodically.
    # The above syntax corresponds to the job schedule at 0(zero) min and 0(zero) hour of every day
    # You can [learn more](https://gutsytechster.wordpress.com/2019/06/24/how-to-setup-a-cron-job-in-django/) about schedule of cronjob 
    
```
You can get your FCM_SERVER_KEY key from
* Go to [firebase](firebase.google.com)
* login
* Go to console
* Add project
* Project settings
* Cloud messaging
* click on "Add server kry"

### Here we will send notification to android device on the basis of hijri date:
Code for sending notifications
```
from django.shortcuts import render
from rest_framework import viewsets
from .serializers import FCMDeviceSerializer
from fcm_django.models import FCMDevice
from datetime import date
from ummalqura.hijri import Umalqurra
import time
import datetime 
from ummalqura.hijri_date import HijriDate

class Ramzanalert():
    def run(self):
        devices = FCMDevice.objects.all()
        if date.today().weekday():
            devices.send_message("Ramzan Alert", "Narrated Abu Huraira: I heard Allah's Apostle saying, Before Allah created the creations, He wrote a Book (wherein He has written): My Mercy has preceded my Anger. and that (Book) is written with Him over the Throne.")
        um = HijriDate.today()
        if HijriDate.current_month()==12:
            if um.day-1 == 1:
                if date.today().weekday():
                    devices.send_message("Ramzan Alert","you can fast on first 9 days of Zilhajj")
            elif um.day-1 == 10:
                if date.today().weekday():
                    devices.send_message("Ramzan Alert","fasting on 10th 11th 12th Zilhajj is forbidden by our prophet(PBUH)")
            elif um.day-1 != 10 and um.day-1 != 1 :
                if date.today().weekday() == 3 or date.today().weekday() == 0:
                    devices.send_message("Ramzan Alert","Aishah narrated:The Prophet used to try to fast on Mondays and Thursdays. [Tirmidhi, Nasai, and Ibn Majah].It is Sunnah to fast today")
                elif date.today().weekday() == 4:
                    devices.send_message("Ramzan Alert","you should not fast Friday alone, but in combination with Thursday or Saturday.")
        elif HijriDate.current_month()==1:
            if um.day-1 == 9:
                if date.today().weekday():
                    devices.send_message("Ramzan Alert","It is Sunnah to fast on 9th and 10th muharram")
            else:
                if date.today().weekday() == 4:
                    devices.send_message("Ramzan Alert","you should not fast Friday alone, but in combination with Thursday or Saturday.")
                elif date.today().weekday() == 3 or date.today().weekday() == 0:
                    devices.send_message("Ramzan Alert","Aishah narrated:The Prophet used to try to fast on Mondays and Thursdays. [Tirmidhi, Nasai, and Ibn Majah].It is Sunnah to fast today")
        elif HijriDate.current_month()==8:
            if um.day-1 == 1:
                devices.send_message("Ramzan Alert","It is Sunnah to fast on first 15 days of Shaban")
            else:
                if date.today().weekday() == 4:
                    devices.send_message("Ramzan Alert","you should not fast Friday alone, but in combination with Thursday or Saturday.")
                elif date.today().weekday() == 3 or date.today().weekday() == 0:
                    devices.send_message("Ramzan Alert","Aishah narrated:The Prophet used to try to fast on Mondays and Thursdays. [Tirmidhi, Nasai, and Ibn Majah].It is Sunnah to fast today")
        elif HijriDate.current_month()== 10:
            if um.day-1 == 1:
                if date.today().weekday():
                    devices.send_message("Ramzan Alert","fasting is prohibited on beginning the day after Eid ul-Fitr.These six days of fasting together with the Ramadan fasts.")
            else:
                if date.today().weekday() == 4:
                    devices.send_message("Ramzan Alert","you should not fast Friday alone, but in combination with Thursday or Saturday.")
                elif date.today().weekday() == 3 or date.today().weekday() == 0:
                    devices.send_message("Ramzan Alert","Aishah narrated:The Prophet used to try to fast on Mondays and Thursdays. [Tirmidhi, Nasai, and Ibn Majah].It is Sunnah to fast today")

        elif HijriDate.current_month()== 9:
            if um.day-1 == 1:
                if date.today().weekday():
                    devices.send_message("Ramzan Alert","During the entire month of Ramadan, Every qualified Muslims is obligated to fast")

        else:
            if um.day-1 == 13:
                if date.today().weekday():
                    devices.send_message("Ramzan Alert","you can fast on 13th,14th and 15th days of moon")
            else:
                if date.today().weekday() == 4:
                    devices.send_message("Ramzan Alert","you should not fast Friday alone, but in combination with Thursday or Saturday.")
                elif date.today().weekday() == 3 or date.today().weekday() == 0:
                    devices.send_message("Ramzan Alert","Aishah narrated:The Prophet used to try to fast on Mondays and Thursdays. [Tirmidhi, Nasai, and Ibn Majah].It is Sunnah to fast today")
obj = Ramzanalert()
obj.run()

```
### Adding cronjobs:
you can add cronjobs by running command 
```
python manage.py crontab add
```
### removing cronjobs:
you can remove cronjobs by running command 
```
python manage.py crontab remove
```
### show cronjobs:
you can see cronjobs by running  command 
```
python manage.py crontab show
```

## Sending notifications to devices:
You can read about different types of messages [here](https://firebase.google.com/docs/cloud-messaging/concept-options)

### Sending messages
```
from fcm_django.models import FCMDevice

device = FCMDevice.objects.all().first()

device.send_message("Title", "Message")
device.send_message(data={"test": "test"})
device.send_message(title="Title", body="Message", icon=..., data={"test": "test"})
```
### Sending messages in bulk

```
from fcm_django.models import FCMDevice

devices = FCMDevice.objects.all()

devices.send_message(title="Title", body="Message")
devices.send_message(title="Title", body="Message", data={"test": "test"})
devices.send_message(data={"test": "test"})

```
## FCM with Django REST Framework 
* ``` FCMDeviceViewSet ```
    * Permissions as specified in settings (AllowAny by default, which is not recommended)
    * A device may be registered without associating it with a user
    * Will not allow duplicate registration_id's

### Routers
Routers include all views

```
from fcm_django.models import FCMDevice
from fcm_django.api.rest_framework import FCMDeviceViewSet
from rest_framework import routers

router = routers.DefaultRouter()
router.register('devices', FCMDeviceViewSet)

urlpatterns = patterns('',
        url('', include(router.urls)),
)

```
## Python 3 support
fcm-django is fully compatible with Python 3.4 & 3.5 & 3.6 & 3.7



