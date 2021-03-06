# Django lessons 3 of 5


# Django Views & Templates 
This lesson will build off of the lesson on Django models and migrations and
wrap up everything we need to build basic applications with Django. Today, we
are going to look at how to actually display our data using views

Then, we will convert it into JSON data!

## Prerequisites

- Python
- SQL & PostgreSQL
- Django Models & Migrations
- Virtual environments

## Learning Objectives

By the end of this, developers should be able to:

- Use Django to create views
- Use routing in Django
- Create templates using Django
- Complete CRUD functionality in Django

## Review

In the previous lesson, we learned how Django deals with data using models. We
updated our `settings.py` file with the `DATABASE` and `INSTALLED APPS`. We
utilized the `models.py` file to define our schema, set our data types and
require constraints. We used our `admin.py` file to allow our superuser to
perform CRUD on our application in the Django UI. There are a lot of other files
here that we haven't used.

### You Do: Django Application File Review (5 min)

Take a few minutes to look through the files in our application, knowing what
you learned in the last lesson. What have we done so far? What files will we
need in today's lesson?

### Before We Start

Navigate to the directory in your sandbox where you've been following along as
we built out Tunr. Activate the virtual environment you created earlier with the
following command:

```sh
pipenv shell
```

## View Functions

Using the `Artist` and `Song` models that we have already implemented, let's
create templates to display our application's data! Views in Django are similar to
controllers in Express. They pass data to our templates.

```python
# tunr/views.py
from django.shortcuts import render

from .models import Artist, Song

def artist_list(request):
    artists = Artist.objects.all()
    return render(request, 'tunr/artist_list.html', {'artists': artists})
```

On the first line, you will see that `render` is already imported. This function
is super helpful, and it does exactly what it sounds like - it renders views!
Next, we have imported our models, `Artist` and `Song`.

Let's break the function down a bit:

- The declaration of the function looks like any other Python function! The only parameter passed into it is the request, which is exactly what it sounds like. This represents the HTTP Request dictionary.
- Next, we are selecting all of the artists from the database into a QuerySet called artists.
- On the third line, we see that we are rendering a template. The first argument is the request argument. The second is the template that we want to render, and the third is a dictionary (which is Python's equivalent to an object!) with the data we want to send to the template. In this case, we are sending the artist QuerySet with the key 'artists'.

### You Do: Song List Function

Write the view and the url to list all of the songs in the application.

<details>
<summary>Solution: Song List Function</summary>

```python
def song_list(request):
    songs = Song.objects.all()
    return render(request, 'tunr/song_list.html', {'songs': songs})
```

</details>

## Running into "Class has no objects member" & "Missing Docstring" Linting Errors?
Here's how to fix those in VSCode.

1. Install pylint-django by running this command in your project root: `$ pipenv install pylint-django`
1. Then in VSCode, open up your user `settings.json` file by pressing `Command + Shift + P` and selecting `Preferences: Open Settings (JSON)`.
1. Add the following key-value pair to the object in `settings.json`:

	```json

	  "python.linting.pylintArgs": [
	    "--load-plugins=pylint_django",
	    "--disable=missing-docstring,invalid-name"
	  ]

	```
1. Press `Command + S` to save your changes. The linting errors should now disappear!

## URLs

Let's go ahead at how we will access these views -- through the URLs!

In Django, the URL's deviate from the ones we've seen in other frameworks. They
use stricter parameters where we have to specify the types of parameters. This
eliminates a ton of the issues we've seen where we've had to reorder urls, but
it makes them a bit more complicated.

Let's look at the existing `urls.py` in the `tunr_django` directory. In there,
let's add a couple things.

```python
# tunr_django/urls.py
from django.conf.urls import include
from django.urls import path
from django.contrib import admin

urlpatterns = [
    path('admin', admin.site.urls),
    path('', include('tunr.urls')),
]
```

On the first line, we are adding an import - `include` - so that we can include
other url files in our main one. We are doing this in order to make our app more
modular. These "mini apps" in Django are supposed to plug into another parent
app if needed, and modularity makes this possible.

Next, Let's write our urls for our app in another file in the `tunr` directory.
Create a file called called `urls.py` and paste the following code block into
it.

```python
# tunr/urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('', views.artist_list, name='artist_list'),
]
```

The path takes three arguments:

- The first argument represents the URL path. Here, the artist list is going to be rendered in the root URL. Similar to the "/" URL in other languages, the path of the root URL starts, ends, and has nothing in between. In Django, we do not even need to include the `/`. This way of doing URLs is great because they are explicit. We no longer have to reorder or rename URLs in order to make them work!
- The URL's second argument is the view function this route is going to match up with in the view file. So, at the root URL, the application will run the `artist_list` function we wrote in `views.py`.
- Thirdly, we are going to use a named parameter. This is going to be referenced in our templates in order to link from one page to another.

### You Do: Song List URL

Add a URL to `tunr/urls.py` for the Song List.

<details>
<summary>Solution: Song List URL</summary>

```python
urlpatterns = [
    path('', views.artist_list, name='artist_list'),
    path('songs/', views.song_list, name='song_list')
]
```

</details>

## Templates and Django Templating Language -> Do this via copy and paste, and don't worry too much about the back end of it


Now that we have two URLs, let's finish up by writing the templates to render
our views! Django has its own templating language used for rendering, which its own syntax.

In the `tunr` directory, add a `templates` directory and a `tunr` subdirectory.
Here, create a file called `artist_list.html` and add the following code. In
your browser, navigate to `localhost:8000` and see what appears!

> NOTE: If you do not have your server running already, run the command `python3 manage.py runserver` in the virtual environment of your project folder in your
> terminal.

```html
<!-- tunr/templates/tunr/artist_list.html -->
<h2>Artists <a href="">(+)</a></h2>
<ul>
  {% for artist in artists %}
  <li>
    <a href="">{{ artist.name }}</a>
  </li>
  {% endfor %}
</ul>
```

What's happening here?

- Right now, we will keep our `href` values blank until we have more routes. We will eventually add paths to URLS here, as we create them throughout this lesson.
- The Django template here loops through our QuerySet of artists, rendering the name of each.
- The distinction between `{{}}` and `{%%}` usually is the difference between rendering or just running code (i.e. if's or for's). The one exception is with url's - which we will see later on today. Formally, this is the distintion between [variables](https://docs.djangoproject.com/en/dev/topics/templates/#tags) and [tags](https://docs.djangoproject.com/en/dev/topics/templates/#tags) in Django's template language.

### You Do: Song List Template

Add a template file `song_list.html` to render the Song List. When you open the
song list in your browser, what path will you use?

<details>
<summary>Solution: Song List URL</summary>

```html
<!-- tunr/templates/tunr/song_list.html -->
<h2>Songs</h2>
<ul>
  {% for song in songs %}
  <li>
    <a href="">{{ song.title }}</a>
  </li>
  {% endfor %}
</ul>
```

</details>

## We Do: Artist Show

Now that we have successfully rendered the artist and song lists, let's add
another route. This time, let's add a show page for each of our two models
starting with views. In the `tunr/views.py` file, add the following code:

```python
# tunr/views.py
def artist_detail(request, pk):
    artist = Artist.objects.get(id=pk)
    return render(request, 'tunr/artist_detail.html', {'artist': artist})
```

This function is similar to the list view. This time, we are selecting one
artist instead of all of them. To do this, we receive a second parameter to the
function, the primary key of the artist we want to display. Let's look at where
that is coming from and connect the URL to the view in `urls.py`.

```python
# tunr/urls.py
path('artists/<int:pk>', views.artist_detail, name='artist_detail'),
```

In the show url we have added `<int:pk>` to the path in the first argument. In
Django, we use `pk` as an alternate term for `id`. In the database, primary keys
are the unique ids for each row, and Django adopts that terminology by
convention. The second argument directs us to the `artist_detail` function in
`views.py`. The third argument used a named parameter to be referenced in our
templates.

Finally, let's write our template.

```html
<!-- tunr/templates/tunr/artist_detail.html -->
<h2>{{ artist.name }} <a href="">(edit)</a></h2>
<h4>{{ artist.nationality }}</h4>

<img src="{{ artist.photo_url }}" alt="" class="artist-photo" />

<h3>Songs <a href="">(+)</a></h3>
<ul>
  {% for song in artist.songs.all %}
  <li>
    <a href="">{{ song.title }}-{{ song.album }}</a>
  </li>
  {% endfor %}
</ul>
```

Here, we are showing some attributes of the artist object, then we are looping
through the songs attached to that artist.

Let's take a step back to our `artist_list.html`. Before, we omitted the links
to to the next page. Now that we have created our show page, let's add its URL
tag to the `href` attribute.

In the `href` attribute, let's add a URL tag. First, we use the name of the URL
that we declared back in the `url` file. Then we need to pass the primary key of
that artist into that url. Update your `artist_list.html` file with the
following code:

```html
<!-- tunr/templates/tunr/artist_list.html -->
<a href="{% url 'artist_detail' pk=artist.pk %}">
  {{ artist.name }}
</a>
```

### You Do: Song Show

Create the show page for the song. Also, link to this view in both the
`artist_detail` and `song_list` templates.

<details>
<summary>Solution: Song Show View in `tunr/views.py`</summary>

```python
def song_detail(request, pk):
    song = Song.objects.get(id=pk)
    return render(request, 'tunr/song_detail.html', {'song': song})
```

</details>
<details>
<summary>Solution: Song Show URL in `tunr/urls.py`</summary>

```python
path('songs/<int:pk>', views.song_detail, name='song_detail')
```

</details>
<details>
<summary>Solution: Song Show Template in `tunr/templates/tunr/song_detail.html`</summary>

```html
<h2>{{ song.title }} <a href="">(edit)</a></h2>
<h3>By: {{ song.artist.name }}</h3>

Album: {{ song.album }}
```

</details>
<details>
<summary>Solution: Artist Show Template Update in `tunr/templates/tunr/artist_detail.html`</summary>

```html
<a href="{% url 'song_detail' pk=song.pk %}">
  {{ song.title }}-{{ song.album }}
</a>
```

</details>
<details>
<summary>Solution: Song List Template Update in `tunr/templates/tunr/song_list.html`</summary>

```html
<a href="{% url 'song_detail' pk=song.pk %}">
  {{ song.title }}
</a>
```

</details>



## We Do: Artist Create -> Important! 
So far we've just shown our artists. Let's now create a new one! First, create
a file called `forms.py` in the `tunr` directory. This is going to be where we
make our forms using Python code.

```python
# tunr/forms.py
from django import forms
from .models import Artist, Song

class ArtistForm(forms.ModelForm):

    class Meta:
        model = Artist
        fields = ('name', 'photo_url', 'nationality',)
```

Let's break this down:

- First, we will create a class to house our form.
- Inside of it we will declare another class. The `Meta` class within the form contains meta data we have to describe the form. In this case, the model attached to the form and the fields we want to include in our form.
- Note that the fields are in parenthesis instead of square brackets - they are in a tuple instead of a list! Tuples are like lists but they are immutable. They can't be changed.

The rationale for the `Meta` class within the class is that it contains
information describing the class that isn't specific to a particular instance,
rather it just contains configuration details.

(Bonus: [You can do this for models as well](https://docs.djangoproject.com/en/1.11/ref/models/options/)).

For clarification (and maybe interview) purposes, this is different than
a Python `metaclass`. They deal with meta-programming in Python!

In our `views.py` file, let's add functionality to create an artist:

```python
# tunr/views.py
from django.shortcuts import render, redirect

from .forms import ArtistForm

def artist_create(request):
    if request.method == 'POST':
        form = ArtistForm(request.POST)
        if form.is_valid():
            artist = form.save()
            return redirect('artist_detail', pk=artist.pk)
    else:
        form = ArtistForm()
    return render(request, 'tunr/artist_form.html', {'form': form})
```

What happening here?

- We must change the first line of our file to `from django.shortcuts import render, redirect` so that we have redirects available to us.
- We must import our ArtistForm class
- This function is a little bit different than what we have seen so far. Instead of having different functions that handle different types of requests, we can handle multiple types within the same function in Django. So, in the first line we check to see if our request is a post request. If it is, we will fill in our form with data from the post request, and check if the form is valid. If it is valid, then we will save the new artist and redirect to it's show page. If it errors, then we will render the artist form with those errors.
- If instead the request method is 'get', we just create an instance of the form without any pre-filled data, and then we will render the form template.

Next, add a url:

```python
# tunr/urls.py
path('artists/new', views.artist_create, name='artist_create'),
```


When we declare our form tags in html we need to have our `csrf_token`, and then
we can just insert our form like `{{ form.as_p }}`. The `.as_p` just formats the
form nicely, you could also just do `{{ form }}` but it would be ugly. We also
need a submit button and then we are good! Errors are handled for us in-line!

Watch this 3-minute video about [csrf attacks](https://www.youtube.com/watch?v=m0EHlfTgGUU).
Read about how [csrf tokens](https://portswigger.net/web-security/csrf/tokens) can be used 
to prevent such attacks.

In our `artist_list.html` file, update the `href` to include a path to our
`artist_create` url.



<details>
<summary>Solution: Song Create Form in tunr/forms.py</summary>

```python
from django import forms
from .models import Artist, Song

class SongForm(forms.ModelForm):

    class Meta:
        model = Song
        fields = ('title', 'album', 'preview_url', 'artist',)
```

</details>
<details>
<summary>Solution: Song Create View in tunr/views.py</summary>

```python
from .forms import ArtistForm, SongForm

def song_create(request):
    if request.method == 'POST':
        form = SongForm(request.POST)
        if form.is_valid():
            song = form.save()
            return redirect('song_detail', pk=song.pk)
    else:
        form = SongForm()
    return render(request, 'tunr/song_form.html', {'form': form})
```

</details>
<details>
<summary>Solution: Song Create URL in tunr/urls.py</summary>

```python
path('songs/new', views.song_create, name='song_create')
```

</details>


## We Do: Artist Edit

Django makes forms modular and reusable, so all we have to do is add a new view
function and a url to make our form edit instead of create. Add the following
code to the `views.py` file.

```python
# tunr/views.py
def artist_edit(request, pk):
    artist = Artist.objects.get(pk=pk)
    if request.method == "POST":
        form = ArtistForm(request.POST, instance=artist)
        if form.is_valid():
            artist = form.save()
            return redirect('artist_detail', pk=artist.pk)
    else:
        form = ArtistForm(instance=artist)
    return render(request, 'tunr/artist_form.html', {'form': form})
```

The only additionally thing we are doing here is adding the instance of the
artist as a named parameter to our ArtistForm class. Voil??! We have an edit form
now! We are rendering the same template and everything!

Next, add a new url:

```python
# tunr/urls.py
path('artists/<int:pk>/edit', views.artist_edit, name='artist_edit'),
```

Do the same thing for the song edit form!

<details>
<summary>Solution: Song Edit View in tunr/views.py</summary>

```python
def song_edit(request, pk):
    song = Song.objects.get(pk=pk)
    if request.method == "POST":
        form = SongForm(request.POST, instance=song)
        if form.is_valid():
	    song = form.save()
            return redirect('song_detail', pk=song.pk)
    else:
        form = SongForm(instance=song)
    return render(request, 'tunr/song_form.html', {'form': form})
```

</details>
<details>
<summary>Solution: Song Edit URL in tunr/urls.py</summary>

```python
path('songs/<int:pk>/edit', views.song_edit, name='song_edit')
```

</details>


## Artist Delete

Delete functions are really simple as well.

```python
# tunr/views.py
def artist_delete(request, pk):
    Artist.objects.get(id=pk).delete()
    return redirect('artist_list')
```

We just need to find an artist, delete it, and then redirect to the index page.

Let's add the url:

```python
# tunr/urls.py
path('artists/<int:pk>/delete', views.artist_delete, name='artist_delete'),
```


### Song Delete

Do the same thing for Songs!

<details>
<summary>Solution: Song Delete View in tunr/views.py</summary>

```python
def song_delete(request, pk):
    Song.objects.get(id=pk).delete()
    return redirect('song_list')
```

</details>
<details>
<summary>Solution: Song Delete URL in tunr/urls.py</summary>

```python
path('songs/<int:pk>/delete', views.song_delete, name='song_delete')
```

</details>

## Now that we have our data viewable on the screen, lets convert it into JSON format!
	
	
	
	
## Additional Resources

- [Django Docs: Templates](https://docs.djangoproject.com/en/2.1/topics/templates/)
- [Django Docs: The Django Templating Language](https://docs.djangoproject.com/en/2.1/ref/templates/language/)
- [Django Docs: Writing Views](https://docs.djangoproject.com/en/2.1/topics/http/views/)
- [Django Docs: Class Based Views](https://docs.djangoproject.com/en/2.1/topics/class-based-views/)

