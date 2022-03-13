ToDo Web App using Python3 Django
======

## Project Setup in CMD

`cd C:\Users\shint\PycharmProjects\Django_Project`

`mkdir To_Do_Application`

`cd To_Do_Application`

`mkvirtualenv todo`

`pip install django`

`django-admin startproject To_Do_Project`

`cd To_Do_Project`

`python manage.py startapp To_Do_App`

## Project Setup in PyCharm
- Open PyCharm and access the project folder
- Access the virtual env using the command: `workon todo` 
- Start the application: `python manage.py startapp To_Do_App`

- Inside the settings.py file of the To_Do_Project, add the app_name under Installed_Apps.

- Set the DIRS path under Templates section of settings.py
`'DIRS': [os.path.join(BASE_DIR, 'TEMPLATES')]`

- connect the urls of movie_project with movie_app
  ==To_Do_Project.urls:==
  `path('', include("To_Do_App.urls"))`

  ==To_Do_App.urls:==
  ```
  from django.urls import path
  from . import views
  urlpatterns = [
    path('',views.index, name='index'),
  ]
  ```
- Open views.py of the app and create the required functions
`return render(request,"contact.html")`

### Create Models:
- To_Do_App: models.py
```
from django.db import models

class Task(models.Model):
  name = models.CharField(max_length=250)
  priority = models.IntegerField()
  def __str__(self):
    return self.name
```
- Register the Models:
admin.py:
```
from . models import Task
admin.site.register(Task)
```
- Apply the changes made to the model
```  
python manage.py makemigrations
python manage.py migrate
```
- Create the form inside home.html file in Templates folder to access the input for the model
``` 
<form method='POST'>
  {% csrf_token %}
  <input type="text" name="task" placeholder="Task Name">
  <input type="text" name="priority" placeholder="Enter your priority">
  <input type="submit">
</form>
```
### Admin Panel Setup:
>Create super user for the project:
`python manage.py createsuperuser`
  Username: admin
  email: admin@admin.com
  password: admin


### Add Task to pass values into database:
Views.py:
```
from . models import Task
def add(request):
  if request.method=='POST':
    name = request.POST.get('task','')
    priority = request.POST.get('priority','')
    task = Task(name=name, priority=priority)
    task.save()
  return render(request,'home.html')
```  
#### Creating Base Template:
- create base.html file
- add bootstrap cdn for css
- add block content 
  ```
  {% block content %}
  {% endblock %}
  ```
- extend the base into the required pages
`{% extends 'base.html' %}`

- Add Boostrap Contents- Form Control
  ```
  {% block content %}
      <div class="container">
        <div class="row">
            <div class="col-md-6">
                <form method='POST' class="mb-3 shadow">
                  {% csrf_token %}
                  <div class="form-group">
                      <br> <br>
                      <label for="task" class="form-label">Task Name</label>
                      <input type="text" class="form-control" name="task" placeholder="Task Name">
                      <br>
                  </div>
                  <div class="form-group">
                      <label for="priority" class="form-label">Priority</label>
                      <input type="text" class="form-control" name="priority" placeholder="Enter your priority">
                      <br>
                  </div>
                  <div class="form-group">
                    <input type="submit" class="btn btn-success">
                  </div>
                </form>
            </div>
        </div>
      </div>
  {% endblock %}
  ```
#### Add a new page to display the contents of the database
  - urls.py:
    `path('details/',views.details, name='details'),`

  - views.py
    ```
    def details(request):
      task=Task.objects.all()
      return render(request,'detail.html',{'task':task})
    ``` 
  - create detail.html template
    ```
    {% for i in task %}
    {{i.name}}
    {{i.priority}}
    {% endfor %}
    ```

#### Display the contents of the database on the same page
  - views.py:
    ```
    from . models import Task
    def add(request):
      task_details=Task.objects.all()
      if request.method=='POST':
        name = request.POST.get('task','')
        priority = request.POST.get('priority','')
        task = Task(name=name, priority=priority)
        task.save()
      return render(request,'home.html',{'task_details':task_details})
    ``` 
  - home.html: 
    ```
      {% for i in task_details %}
      {{i.name}}
      {{i.priority}}
      {% endfor %}
    ```
   - Adding Style:
      - create card for task details
        ```
        {% for i in task_details %}
          <div class="card" style="width: 18rem;">
            <div class="card-body">
              <h5 class="card-title">{{i.name}}</h5>
              <p class="card-text">{{i.priority}}</p>
              <a href="#" class="btn btn-danger">Done</a>
            </div>
          </div>
        {% endfor %}
        ```

      - create banner from style.css in static folder
      home.html:
        ```
          <div class="container-fluid">
            <div class ="row">
              <div class="col-md-12">
                <div class="banner">
                  <div class="banner-text">
                    <h1>ToDo</h1>
                  </div>
                </div>
              </div>
            </div>
          </div>
        ```
      - Load Static files:
        settings.py:
        In the section, STATIC_URL = '/static/' add the required changes.
        ```
        STATICFILES_DIRS = [os.path.join(BASE_DIR), 'static']
        STATIC_ROOT = os.path.join(BASE_DIR, 'assets')
        ```
        base.html:
        ```
        {% load static %}
        <link rel="stylesheet" href="{% static 'style.css' %}">
        ```
        create style.css file inside static folder


#### Delete task from the list while clicking on Done button
  - views.py:
      ```
      def delete(request, taskid):
          task=Task.objects.get(id=taskid)
          if request.method == 'POST':
              task.delete()
              return redirect('/')
          return render(request, 'delete.html')
      ```    
  - urls.py
          `path('delete/<int:taskid>/',views.delete, name='delete'),`
        
  - delete.html
      ```
        <form method='POST' class="p-3 shadow">
          {% csrf_token %}
            <div class="form-group"><br>
              <div class="shadow card text-dark bg-light border-warning mb-3" >
                <div class="card-body">
                  <h5 class="card-title">Are you sure to mark this task as Completed?</h5>
                </div>
              </div>
              <button type="button" class="btn btn-danger">Delete</button><br>
            </div>
        </form>
      ```              

#### Add Datefield to the data:
  - models.py:
    `date=models.DateField()`

  - Make the required changes in home.html

  - Terminal:
    ```
    python manage.py makemigrations
    python manage.py migrate
    ```

#### Update data in the database:
  - views.py:
    ```
    from . forms import TodoForm

    def update(request, id):
        task = Task.objects.get(id=id)
        f = TodoForm(request.POST or None, instance=task)
        if f.is_valid():
            f.save()
            return redirect('/')
        return render(request, 'edit.html', {'f': f, 'task': task})
    ```
  - create forms.py inside the app folder
    ```
    from . models import Task
    from django import forms


    class TodoForm(forms.ModelForm):
        class Meta:
            model=Task
            fields=['name','priority','date']
    ```
  - Create edit.html file
    ```
    <form method='POST' class="p-3 shadow">
      {% csrf_token %}
      <!--{{f.as_p}}-->
        <div class="form-group">
          <label for="name" class="form-label">Update Task Name</label>
          <input type="text" class="form-control" name="name" value="{{task.name}}"><br>
        </div>
        <div class="form-group">
          <label for="priority" class="form-label">Update Priority</label>
            <input type="text" class="form-control" name="priority" value="{{task.priority}}"><br>
        </div>
        <div class="form-group">
            <label for="date" class="form-label">Update Date</label>
            <input type="date" class="form-control" name="date" value="{{task.date|date:'Y-m-d'}}"><br>
        </div>
        <div class="form-group">
          <input type="submit" class="btn btn-success">
        </div>
    </form>
     ```           
  - urls.py
      `path('update/<int:id>/', views.update, name='update'),`


### Class based Generic Views:
- List View
  -  views.py
    ```
    from django.views.generic import ListView


    class TaskListview(ListView):
        model = Task
        template_name = 'home.html'
        context_object_name = 'task_details'
    ```  
   - urls.py
    `path('cbvhome/',views.TaskListview.as_view(),name='cbvhome'),`
    
  
- Detail View
  - views.py
    ```
    from django.views.generic.detail import DetailView


    class TaskDetailview(DetailView):
        model = Task
        template_name = 'detail.html'
        context_object_name = 'task'
    ```
  - urls.py
        `path('cbvdetail/<int:pk>/',views.TaskDetailview.as_view(),name='cbvdetail'),`
        

- Update View
  - views.py
    ```
    from django.views.generic.edit import UpdateView

    class TaskUpdateview(UpdateView):
        model = Task
        template_name = 'update.html'
        context_object_name = 'task'
        fields = ('name','priority','date')

        def get_success_url(self):
            return reverse_lazy('cbvdetail',kwargs={'pk':self.object.id})
    ```    
  - create update.html page

  - urls.py
      `path('cbvupdate/<int:pk>/', views.TaskUpdateview.as_view(), name='cbvupdate'),`
      


- Delete View
  - views.py
    ```
    from django.views.generic.edit import DeleteView


    class TaskDeleteview(DeleteView):
        model = Task
        template_name = 'delete.html'
        success_url = reverse_lazy('cbvhome')
    ```
  - urls.py
        `path('cbvdelete/<int:pk>/', views.TaskDeleteview.as_view(), name='cbvdelete'),`
        
