# Python Live Project Code Summary
## Introduction
The following code was writen during my two month live project during my time in The Tech Academy software developer boot 
camp. This specific project was to build a web centered around python and the django framework. This live project was my
first taste of what it was like to work on a coordinated development team and actively set and achieve effective goals. 
This was also the most rewarding and challenging part of the boot camp. Having daily scrum meetings was a fantastic 
opportunity to get to articulate issues and questions and practice effective communication. I was incredibly fortunate 
to get experience working with a few other subjects that we were unable to cover in greater detail. Routinely during the
boot camp, I would have to research solutions to problems. This was also true of the live project to a much greater degree.
There was this imperative drive I received during the live project that made my need to research so much more crucial. 
Being able to solve my own problems in an actual group setting will directly effect the flow and success of a team. 
I also loved the opportunity to get to see the issues my teammate had and how they overcame them.

I'd like to dive into the specifics of challenges I've faced and things I've learned a bit more.

### First Week

The beginning of my first week in the live project started off with a few simple tasks. Even 
though I was already familiar with the work load, coming into a project that was already underway
made me have to reevaluate how to solve those problems. On a blank slate the answer may be straight 
forward but when code that had already been established has errors or a botched mergers breaks 
previously working code, it can become a whole new problem. 

##### The Problem #1

My first task on the Python live project was simple. I had to fix a broken link leading to the space launch scraping app
portion of the program. We did not initially know the reason for the broken link so I had to step through the code 
to find where the break was. 

##### The Solution #1

At some point, the urls page for the space app was deleted. In addition to that, the url file for the project also had
the path leading to the space app delete. In the code block below, I used three asterisks to note the code that I added
in the import statements and the url path that I added to direct the program to the urls page for the space app. 


    from django.conf.urls.static import static
    from AccountsApp.views import ProfileView, EventCalendar #NZ
    from django.contrib.auth import views as auth_views #this is for the password reset views
    ***from SpaceApp import views***


    path('news/', include('AppDemoNews.urls')),
    path('message', include('MessagesApp.urls')), # Path to message
    path('profile/', ProfileView.as_view(), name='profile'),
    ***path('space/', include('SpaceApp.urls')), # Path to Space***
    path('bands/', include('BandsApp.urls')), #Path to bandsApp -KW
    path('podcast/', include('PodcastsApp.urls')), # Path to top 5 stitcher podcasts
    path('news/', include('AppDemoNews.urls')),
    
The below is the code from the space app url page I wrote to direct the program to the correct html page.

    		
    from django.urls import path # copied from BandsApp urls
    from . import views 
    # copied from BandsApp urls
    # route to currentapod HTML file 
    urlpatterns = [
        path('', views.currentapod, name='currentapod'),
    ]
   
This was a simple story and good practice to reacquaint me with Django.

##### The Problem #2
    
The next story was a fun one. This one was another case of having to fix a portion of the project that was once working
and suddenly broke. For whatever reason, our movie scraping app was no longer populating our html page with the top 5
IMDB movies. The reason I had so much fun with this story is because I had to go back through the prior commits to when
the app was working properly. I had no clues as to when it was working properly so it really helped me hone my detective
abilities. It also help me become proficient with using Microsoft Azure to explore a project and navigate the past
incarnations of a project.
    
    
##### The Solution #2

I spent a few hours searching through our past commits on Azure attempting to locate the past working version of the
project. Below, marked with three asterisks is the missing code that I eventually located. After this code was added,
the movie scraping app began to work properly again.
    
    from .models import Movie
    import requests
    import re
    ***from bs4 import BeautifulSoup***
    ***import requests***
    ***import re***
    
    # Download IMDB's Top 250 data
    ***url = 'http://www.imdb.com/chart/top'***
    ***response = requests.get(url)***
    ***soup = BeautifulSoup(response.text, 'lxml')***
    ***#Get all necessary information from the IMDb webpage (title, link, cast)***
    ***movies = soup.select('td.titleColumn')***
    ***#creating list to store movie titles***

This was also my first taste of working with an api. I was happy to finally cross that bridge and would get to do so in
later stories as well.

##### The Problem #3
    
My next story was one of the largest and most fulfilling of the Python live project. My task was to clean up the app 
that scrapes data for all the rocket launches happening around the world and to restore functionality to it. It was at
this point I started to realize this project was a bit of a mess so it was great practice for navigating my around
broken parts of a project.
    
##### The Solution #3

I first started with relocating all of the files related to space app and placed them in their corresponding folders.
At first, there was a number of migrations and a launch model in our dataApp folder. I made sure that the movie scraping
app maintained it's functionality after making these changes. At this point, I basically treated the space scraping app
like a brand new app that had not even been created yet. From the command line, I introduced the space app the properly
populate the Django file with our "SpaceApp" folder. After this, I added "SpaceApp" to our installed apps in our 
projects settings.py file. That code snippet is below and noted with three asterisks.

    'AccountsApp',
    'WeatherApp',
    ***'SpaceApp',***
    'TrafficApp',
    'AppDemoNews',

This is when things got fun. I had to find out from scratch how to call the launch api and parse a Json responds, which
I had been waiting to do for some time. The first thing I did was create a function to get the next 5 rocket launches
around the world. Finding the url for the next 5 launches was easy but I had to do some research in regards to reading a
Json object. I discovered that a Json object was essentially a dictionary, which I had some experience with getting
information from before. Since I needed data from the next 5 launches, I created a for loop to iterate over my Json 
responds. Below, you will see each variable is set to the location in the Json object for the data that we required.
It took some trial and error before I was able to get each piece of data correctly. It took me a second to deduce how to
get the data for each launch to the view. I decided to create a dictionary full of dictionaries. You'll see below where 
I declared the "launches_data" dictionary and below the parsing of the Json object where I added each launch to the 
dictionary at the end of each iteration of the for loop. At the end of the function, the "launches_data" dictionary is 
the returned result of the function.
        
        def get_launches():
        
        #launchlibrary API call for next 5 launches
        base_url ='https://launchlibrary.net/1.4/launch/next/5'
        
        #get json object response from API
        #result is a large nested dictionary
        json_data_launch = requests.get(base_url).json()
      
        #declare launches dictionary
        launches_data = {}
      
        #for loop to create each dictionary entry 
        for i in range(0,5):
            #go to launches key at index "i" in json object and retrieve value associated with the key "name".
            json_name = json_data_launch['launches'][i]['name'] 
            #go to launches key at index "i" in json object, then go to key "location", then go to key "pads" and at 
                value index 0, retrieve value associated with the key "name".
            json_location = json_data_launch['launches'][i]['location']['pads'][0]['name'] 
            #go to launches key at index "i" in json object and retrieve value associated with the key "windowstart".
            json_windowstart = json_data_launch['launches'][i]['windowstart']
            #go to launches key at index "i" in json object and retrieve value associated with the key "windowend".
            json_windowend = json_data_launch['launches'][i]['windowend']
            #go to launches key at index "i" in json object, then go to key "lsp", and retrieve value associated with 
                the key "name".
            json_agency_name = json_data_launch['launches'][i]['lsp']['name']
    
            #create dictionary "launch_data" for each individual launch and give each key its value retrieved from the 
                json object. 
            launch_data = {"name" : json_name, "agency" : json_agency_name, "location" : json_location,
                                "windowstart" : json_windowstart, "windowend" : json_windowend}
            #add each "launch_data" dictionary created as a value at key "i" to the dictionary "launches_data"                    
            launches_data[i] = launch_data
    
        #returns launces_data dictionary    
        return launches_data
        
Our app also required us to post the astronomy picture of the day. This portion of the story had actually been completed
before so I just used that code in the new version of the app. From this point, as far as this controller was concerned,
all I had to do was pass the apod data and the launches data into "context" to be passed into the view.
            
        #print(json_name)
        #set input variable from nasa.apod.apod as YYYY-MM-DD
        input = date.today().strftime('%Y-%m-%d')
        picture = apod.apod(input)
    
        # create variables for apod api objects
        title = picture.title
        url = picture.url
        desc = picture.explanation
    
        launches_data = get_launches()
    
        # pass in title, picture URL, pic description and data for launches to context to be passed into HTML page
        context = {'title': title, 'url': url, 'desc': desc, 'launches_data': launches_data}
        return render(request, 'space/apod.html', context)
    
My next step was to move onto the view side of things. A lot of the work was already complete so there was only a few 
adjustments I had to make. Below is the code I used to format the launch data that was passed into the html.
            
            <div class="card" style="padding:3%;">
                <p>
                    {% for key, value in launches_data.items %}
                        
                            <p>
                                <h4> {{ value.name }} </h4>
                                <br>
                                Launch Service Provider: {{ value.agency }}
                                <br>
                                Launch Location: {{ value.location }}
                                <br>
                                Launch Window Between: {{ value.windowstart }} - {{value.windowend}} 
                                <br><br>
                            </p>
                          
                    {% endfor %}            
                </p>
            </div>
            
There would be more work that I discovered need to be done with the space app but this was basically it for this story.
Again, I had a lot of fun with this one.

##### The Problem #4

So the morning after I completed the last story of reworking the space app, I went to go test it again. Everything was
working fine except for the astronomy picture of the day. On that particular day, the picture as actually a video.
I was assigned to keep working on the space app to fix this occurrence. It was at this point I actually went to nasa's
api website to see how they implemented the apod on their end. 

##### The Solution #4
    
I found that they were using an iframe to contain their url. At first we were just using an image tag in the html.
Below is the code block I changed to an iframe type. After this was changed, I was able to dynamically accommodate 
ether video or an image.
       
     <img src="{{url}}" width="100%" height="100%"> 
    
     <div class="iframe-apod-container">
            <iframe  src={{url}} 
                allow="autoplay;"
                allowfullscreen>
            </iframe>
        </div>
        
##### The Problem #5

My last story for the python live project was another fun one. I got to start a calendar app from the ground up. I got
to work with apis more so I was very excited with that. The calendar app was to connect to the users google account 
and let you set and update calendar events.

##### The Solution #5

Starting the app from the command prompt created a handful of files in the CalendarApp folder that I needed to get the
app started. I registered the app in the dataScrape.settings.py page and the dataScrape.urls page. After this, I looked 
to a tutorial from google for guidance for where to start. I had first been given a token.pickle file that I needed to 
keep in the main directory of the dataScrape app. This pickle was associated with my google account specifically. After,
I was given the following code block to be put in the controller for the calendarApp. This block of code sets the 
authentication for the code first. The code then sets the credentials using the token pickle if it exists. It passes the
credentials into the variable "service" along with a request to use the calendar app of version 3.

        # Connect to Google calendar API and declare auth SCOPE
    SCOPES = ['https://www.googleapis.com/auth/calendar']
    
    def GetCredentials():
        creds = None
        # The file token.pickle stores the user's access and refresh tokens, and is
        # created automatically when the authorization flow completes for the first
        # time.
        if os.path.exists('token.pickle'):
            with open('token.pickle', 'rb') as token:
                creds = pickle.load(token)
        # If there are no (valid) credentials available, let the user log in.
        if not creds or not creds.valid:     
            if creds and creds.expired and creds.refresh_token:
                creds.refresh(Request())
            else:
                flow = InstalledAppFlow.from_client_secrets_file(
                    'credentials.json', SCOPES)
                creds = flow.run_local_server()
            # Save the credentials for the next run
            with open('token.pickle', 'wb') as token:
                pickle.dump(creds, token)
    
        #call Google API for calendar version and attach credentials 
        service = build('calendar', 'v3', credentials=creds)
        return service
    
After this was complete, I needed to retrieve the user data that was being posted from the create calendar event html
form page. The code below collects the data from each field and sets it to a variable. 

    #Process an Event from the Event creation Form
    def post(self, request):
        #Checks if HTTP request method is equale to "POST"
        if request.method == 'POST':
            #If method is equal to "POST", retrieve event form with user entries in each field 
            form = EventForm(request.POST)
            #If call method ".is_valid" on form to check if it is valid
            if form.is_valid():
                data = request.POST.copy() #retrieve data from form at update-calendar.html
                event_summary = data.get('event_title') #assign data in event_title field to variable event_summary
                event_start_date = data.get('start_date') #assign data in start_date field to variable event_start_date
                event_start_time = data.get('start_time') #assign data in start_time field to variable event_start_time
                event_end_date = data.get('end_date') #assign data in end_date field to variable event_end_date
                event_end_time = data.get('end_time') #assign data in end_time field to variable event_end_time
                print(event_summary, event_start_date, event_start_time, event_end_date, event_end_time) #prints summary
                and start and end time and date of newly created event
           
With each variable, I was able to create a the "event" object.
                    
                #Get the event form the Event Form and insert into Google Calendar
                event = {
                    'summary': event_summary,
                    'start': {
                        'dateTime': event_start_date+'T'+event_start_time+'-07:00',
                        'timeZone': 'America/Los_Angeles',
                    },
                    'end': {
                    'dateTime': event_end_date+'T'+event_end_time+'-07:00',
                    'timeZone': 'America/Los_Angeles',
                    },
                    'reminders': {
                        'useDefault': False,
                        'overrides': [
                        {'method': 'email', 'minutes': 24 * 60},
                        {'method': 'popup', 'minutes': 10},
                        ],
                    },
                }
            
With the code block below, I was able to call the "getCredentials" method that is used when calling the API to send the
"event" object to the primary calendar.               
            
                #call GetCredentials and assign returned value to the variable service 
                service = GetCredentials()
                #call the APIs "insert" method and pass in the calendarId and event as aurguments
                event = service.events().insert(calendarId='primary', body=event).execute()
               # print (event.get('htmlLink')) 
                return HttpResponseRedirect('/events-calendar/')       
        else:
            form = EventForm()
            args = {'form':form}
            return render (request, self.template_name, args)
            
The code block below renders the view that will be used as the form that creates the "event" form that we just saw 
above.
            
    class EventCalendar(TemplateView):
        #passes the events-calendar.html into the variable template_name
        template_name = 'CalendarApp/events-calendar.html'
        #Display the Event creation Form
        def get(self,request):
            #create instantiation of the class EventForm and store it in the variable form
            form = EventForm()
            #return html page held in the variable template_name and pass in the varible "form" as "form" 
            return render (request, self.template_name, {'form':form})

The code block below is in the calendarApp.forms.py file to create the form that is used in post method.

    class EventForm(forms.Form):
    event_title = forms.CharField(max_length=100,required=True)
    start_date = forms.DateField(initial=datetime.now(), required=True)
    start_time = forms.TimeField(initial=datetime.now(), required=True)
    end_date = forms.DateField(initial=datetime.now(), required=True)
    end_time = forms.TimeField(initial=datetime.now(), required=True)

The code below is the html file for the "post" method.
           
    {% extends 'base.html' %}
    {% block title %}events-calendar{% endblock %}
    
    {% block content %}
    {% include "nav.html" %}
    <body>
        <div class="container">
            <h1 class="title">
                Google Events Calendar
            </h1>
        </div>
        <br>
        <section class="section">
            <div class="container">
                <form style="color:white;" method="POST">
                    {% csrf_token %}
                            <div class="field has-addons">
                                <div class="control is-expanded">
                                    {{ form.as_ul }}
                                </div>
                                <br>
                                <div class="control">
                                    <button type="submit" class="button is-info">
                                       Submit
                                    </button>
                                </div>
                            </div>
                        </form>
                    </div>
                </div>
                <button onclick="window.location.href = 'update-calendar';">
                        Update Events
                </button>
            </div>
        </section>   
    </body>
    {% endblock %}
                
I also needed to make an update post method for update selected events. This was set up slightly different from the 
create event method
                
    class UpdateCalendar(TemplateView):
        #passes the events-calendar/update-calendar.html into the variable template_name
        template_name = 'CalendarApp/events-calendar/update-calendar.html'
        #Display the Event update Form
        def get(self,request):
            #create instantiation of the class EventForm and store it in the variable form
            form = EventForm()
            #return html page held in the variable template_name and pass in the varible "form" as "form" 
            return render (request, self.template_name, {'form':form})
    
        #Process an Event from the Event creation Form
        def post(self,request):
                    #Checks if HTTP request method is equale to "POST"
            if request.method == 'POST':
                #If method is equal to "POST", retrieve event form with user entries in each field 
                form = EventForm(request.POST)
                #If call method ".is_valid" on form to check if it is valid
                if form.is_valid():
                    data = request.POST.copy() #retrieve data from form at update-calendar.html
                    event_summary = data.get('event_title') #assign data in event_title field to variable event_summary
                    event_start_date = data.get('start_date') #assign data in start_date field to variable event_start_date
                    event_start_time = data.get('start_time') #assign data in start_time field to variable event_start_time
                    event_end_date = data.get('end_date') #assign data in end_date field to variable event_end_date
                    event_end_time = data.get('end_time') #assign data in end_time field to variable event_end_time
                    print(event_summary, event_start_date, event_start_time, event_end_date, event_end_time)
    
                    #call GetCredentials and assign returned value to the variable service 
                    service = GetCredentials()
    
I needed to retrieve the top 20 events associated with the users calendar so I could preform the necessary logic on it.  
    
                    #retrieves 20 events based off the relative calendar being used
                    events_result = service.events().list(calendarId='primary',
                                                        maxResults=20, singleEvents=True,
                                                        orderBy='startTime').execute()
                    #creates list based off the results of service.events().list 
                    events = events_result.get('items', [])
    
Below is the code block that is used to compare the requested event summary data from the update calendar html page.    

                    #creates for loop to iterate over events list
                    for event in events:
                        #if statement to check if the user entry in the field "summary" is equal to the value of the 
                        "summary" key of the current event 
                        if event['summary'] == event_summary:
                            #if the "summary" entered by user matches the "summary" of current event, store that events 
                            "id" in the variable "eventId"
                            eventId = event['id']      
    
The code below gets the requested event that has an event id that matches the event summary from the top 20 closest
calendar events and then creates an adjust based on the event form from the update calendar event html page.
    
                            #call the APIs "get" method and pass in the "calendarId" and "eventId" as aurguments
                            event = service.events().get(calendarId='primary', eventId=eventId).execute()
                            
                            #take the currently existing event and alter the values of the keys according to user imput
                            event = {
                                        'summary': event_summary,
                                        'start': {
                                            'dateTime': event_start_date+'T'+event_start_time+'-07:00',
                                            'timeZone': 'America/Los_Angeles',
                                        },
                                        'end': {
                                            'dateTime': event_end_date+'T'+event_end_time+'-07:00',
                                            'timeZone': 'America/Los_Angeles',
                                        },
                                    }
    
After the updated event object is complete, the code below calls the api with the current event form attached to the
"updated_event" object along with the eventId being updated, the credentials and the primary calendarId.
    
                            #call the APIs "update" method and pass in the calendarId, eventId and event info as 
                            aurguments
                            updated_event = service.events().update(calendarId='primary', eventId=eventId, body=event)
                            .execute()
                            # Print the updated date to the console.
                            print (updated_event['updated'])
                            #redirect user back to the events-calendar html page.
                            return HttpResponseRedirect('/events-calendar/')    
                            
Below is the code in the update-calendar html page. It is very similar to the create calendar event page.

            {% extends 'base.html' %}
    {% block title %}events-calendar-update{% endblock %}
    
    {% block content %}
    {% include "nav.html" %}
    <body>
        <div class="container">
            <h1 class="title">
                Google Events Calendar Update
            </h1>
        </div>
        <br>
        <section class="section">
            <div class="container">
                <form style="color:white;" method="POST">
                    {% csrf_token %}
                            <div class="field has-addons">
                                <h4>Enter name of event to update followed by the new date and time</h4>
                                <div class="control is-expanded">
                                    {{ form.as_ul }}
                                </div>
                                <br>
                                <div class="control">
                                    <button type="submit" class="button is-info">
                                        Update
                                    </button>
                                </div>
                            </div>
                        </form>
                    </div>
                </div>
            </div>
        </section>
    </body>
    {% endblock %}
           
This basically wrapped up the story for creating the calendar app. Everything was functional and the user could both
create and update events to their google calendar from the data scrape website. The only part of this story I was unable
to cover due to the fact that I was moving on from the python protect was that since it was my token.pickle specifically
that was being used in the project directory, only my calender could be used with the app. There was some way to grant
the user a token.pickle from the data scrape website that I was unable to learn able that was the last remaining piece
to getting the calendarApp to function exactly how we wanted it to. Regardless, I learned a lot during this story and
as far as the python project goes, It was my most challenging and fulfilling.
