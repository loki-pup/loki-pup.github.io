---
title: Django Demo Project
teaser: web development
category: Django
tags: [web development]
---

### Flow:

HTTP Request -> urls.py -> forward request to view: views.py -> HTTP Response (HTML)

models.py <-> read / write data views.py

Template (<filename>.html) -> views.py

* View: A view is a request handler function, which receives HTTP requests and returns HTTP responses

* Templates: A template is a text file defining the structure or layout of a file (such as an HTML page), with placeholders used to represent actual content

## Create the project

```
django-admin startproject locallibrary
```

It has the following structure:
```
locallibrary/
    manage.py
    locallibrary/
        __init__.py
        settings.py
        urls.py
        wsgi.py
        asgi.py
```

## Create the catalog application

Make sure to run this command from the same folder as your project's manage.py:

```
py -3 manage.py startapp catalog
```

update structure:
```
locallibrary/
    manage.py
    locallibrary/
    catalog/
        admin.py
        apps.py
        models.py
        tests.py
        views.py
        __init__.py
        migrations/
```

## Register the catalog application

We have to register it with the project so that it will be included when any tools are run (like adding models to the database for example). 

Applications are registered by adding them to the `INSTALLED_APPS` list in the project settings.

```
 # Add our new application
    'catalog.apps.CatalogConfig', # This object was created for us in /catalog/apps.py
```    

> A django App is a fancy name for a python package. Really, that's it. The only thing that would distinguish a django app from other python packages is that it makes sense for it to appear in the INSTALLED_APPS list in settings.py, because it contains things like templates, models, or other features that can be auto-discovered by other django features.

from [What exactly are Django Apps](https://stackoverflow.com/questions/7276011/what-exactly-are-django-apps)

## Specify the database

You can see how this database is configured in settings.py:

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}
```

## Other project settings

The settings.py file is also used for configuring a number of other settings, but at this point, you probably only want to change the TIME_ZONE

```
TIME_ZONE = 'Hongkong'
```

* SECRET_KEY. This is a secret key that is used as part of Django's website security strategy. If you're not protecting this code in development, you'll need to use a different code (perhaps read from an environment variable or file) when putting it into production.

* DEBUG. This enables debugging logs to be displayed on error, rather than HTTP status code responses. This should be set to False in production as debug information is useful for attackers, but for now we can keep it set to True.

## Hook up the URL mapper

The website is created with a URL mapper file (urls.py) in the project folder. While you can use this file to manage all your URL mappings, it is more usual to **defer mappings to the associated application**.

```
The `urlpatterns` list routes URLs to views. For more information please see:
    https://docs.djangoproject.com/en/5.0/topics/http/urls/
Examples:
Function views
    1. Add an import:  from my_app import views
    2. Add a URL to urlpatterns:  path('', views.home, name='home')
Class-based views
    1. Add an import:  from other_app.views import Home
    2. Add a URL to urlpatterns:  path('', Home.as_view(), name='home')
Including another URLconf
    1. Import the include() function: from django.urls import include, path
    2. Add a URL to urlpatterns:  path('blog/', include('blog.urls'))
```

This new item includes a **path() that forwards requests with the pattern catalog/ to the module catalog.urls** (the file with the relative URL catalog/urls.py).

```
# Use include() to add paths from the catalog application
from django.urls import include

urlpatterns += [
    path('catalog/', include('catalog.urls')),
]
```

Now let's redirect the root URL of our site (i.e. 127.0.0.1:8000) to the URL 127.0.0.1:8000/catalog/. This is the **only app** we'll be using in this project. 

To do this, we'll use a special view function, RedirectView, which takes the new relative URL to redirect to (/catalog/) as its first argument when the URL pattern specified in the path() function is matched (the root URL, in this case).

```
# Add URL maps to redirect the base URL to our application
from django.views.generic import RedirectView
urlpatterns += [
    path('', RedirectView.as_view(url='catalog/', permanent=True)),
]
```

Leave the first parameter of the path function empty to imply '/'. If you write the first parameter as '/' Django will give you the following warning when you start the development server:

```
System check identified some issues:

WARNINGS:
?: (urls.W002) Your URL pattern '/' has a route beginning with a '/'.
Remove this slash as it is unnecessary.
If this pattern is targeted in an include(), ensure the include() pattern has a trailing '/'.
```

Django does not serve static files like CSS, JavaScript, and images by default, but it can be useful for the development web server to do so while you're creating your site. As a final addition to this URL mapper, you can enable the serving of static files during development by appending the following lines.

Django does not serve static files like CSS, JavaScript, and images by default, but it can be useful for the development web server to do so while you're creating your site. You can enable the serving of static files during development by appending the following lines.

```
# Use static() to add URL mapping to serve static files during development (only)
from django.conf import settings
from django.conf.urls.static import static

urlpatterns += static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
```

As a final step, create a file inside your catalog folder called urls.py, and add the following text to define the (empty) imported urlpatterns. This is where we'll add our patterns as we build the application.

```
from django.urls import path
from . import views

urlpatterns = [

]
```

## Test the website framework

Before we run the server, we should first run a database migration. This updates our database (to include any models in our installed applications) and removes some build warnings.

### Run database migrations

Django uses an Object-Relational-Mapper (ORM) to map model definitions in the Django code to the data structure used by the underlying database. As we change our model definitions, Django tracks the changes and can create database migration scripts (in /django-locallibrary-tutorial/catalog/migrations/) to automatically migrate the underlying data structure in the database to match the model.

make sure you are in the directory that contains manage.py

```
py -3 manage.py makemigrations
py -3 manage.py migrate
```

> **Warning:** You'll need to run these commands every time your models change in a way that will affect the structure of the data that needs to be stored (including both addition and removal of whole models and individual fields).

### Run the website
During development, you can serve the website first using the development web server, and then viewing it on your local web browser.

> Note: The development web server is not robust or performant enough for production use, but for a convenient quick test. By default it will serve the site to your local computer (http://127.0.0.1:8000/), but you can also specify other computers on your network to serve to.

```
py -3 manage.py runserver
```

## Design the LocalLibrary models

Django web applications access and manage data through Python objects referred to as models. Models define the structure of stored data, including the field types and possibly also their maximum size, default values, selection list options, help text for documentation, label text for forms, etc. The definition of the model is independent of the underlying database — you can choose one of several as part of your project settings. Once you've chosen what database you want to use, you don't need to talk to it directly at all — you just write your model structure and other code, and Django handles all the dirty work of communicating with the database for you.

## Model primer

### Model definition

Models are usually defined in an application's models.py file. They are implemented as subclasses of django.db.models.Model, and can include fields, methods and metadata. 

### Fields

A model can have an arbitrary number of fields, of any type — each one represents a column of data that we want to store in one of our database tables.

```
my_field_name = models.CharField(max_length=20, help_text='Enter field documentation')
```

* max_length=20 — States that the maximum length of a value in this field is 20 characters.

* help_text='Enter field documentation' — helpful text that may be displayed in a form to help users understand how the field is used.

The field name is used to refer to it in queries and templates. Fields also have a **label**, which is specified using the **verbose_name** argument (with a default value of None). If verbose_name is not set, the label is created from the field name by **replacing any underscores with a space, and capitalizing the first letter** (for example, the field my_field_name would have a **default label of My field name** when used in forms).

#### COMMON FIELD ARGUMENTS

* default: The default value for the field. This can be a value or a callable object, in which case the object will be called every time a new record is created.

* null: If True, Django will store blank values as NULL in the database for fields where this is appropriate (a CharField will instead store an empty string). The default is False.

* blank: If True, the field is allowed to be blank in your forms. The default is False, which means that Django's form validation will force you to enter a value. This is often used with null=True, because if you're going to allow blank values, you also want the database to be able to represent them appropriately.

* choices: A group of choices for this field. If this is provided, the default corresponding form widget will be a select box with these choices instead of the standard text field.

* unique: If True, ensures that the field value is unique across the database. This can be used to prevent duplication of fields that can't have the same values. The default is False.

* primary_key: If True, sets the current field as the primary key for the model (A primary key is a special database column designated to uniquely identify all the different table records). If no field is specified as the primary key, Django will automatically add a field for this purpose. The type of auto-created primary key fields can be specified for each app in AppConfig.default_auto_field or globally in the DEFAULT_AUTO_FIELD setting.

#### COMMON FIELD TYPES

* **CharField** is used to define short-to-mid sized fixed-length strings. You must specify the max_length of the data to be stored.

* **TextField** is used for large arbitrary-length strings. You may specify a max_length for the field, but this is used only when the field is displayed in forms (it is not enforced at the database level).

* **IntegerField** is a field for storing integer (whole number) values, and for validating entered values as integers in forms.

* **DateField** and **DateTimeField** are used for storing/representing dates and date/time information (as Python datetime.date and datetime.datetime objects, respectively). These fields can additionally declare the (mutually exclusive) parameters auto_now=True (to set the field to the current date every time the model is saved), auto_now_add (to only set the date when the model is first created), and default (to set a default date that can be overridden by the user).

* **EmailField** is used to store and validate email addresses.

* **FileField** and **ImageField** are used to upload files and images respectively (the ImageField adds additional validation that the uploaded file is an image). These have parameters to define how and where the uploaded files are stored.

* **AutoField** is a special type of IntegerField that automatically increments. A primary key of this type is automatically added to your model if you don't explicitly specify one.

* **ForeignKey** is used to specify a one-to-many relationship to another database model. The "one" side of the relationship is the model that contains the "key" (models containing a "foreign key" referring to that "key", are on the "many" side of such a relationship).

* **ManyToManyField** is used to specify a many-to-many relationship. In our library app we will use these very similarly to ForeignKeys, but they can be used in more complicated ways to describe the relationships between groups. These have the parameter on_delete to define what happens when the associated record is deleted (e.g. a value of models.SET_NULL would set the value to NULL).

### Metadata
You can declare model-level metadata for your Model by declaring class Meta, as shown.

```
class Meta:
    ordering = ['title', '-pubdate']
```

As shown above, you can prefix the field name with a minus symbol (-) to reverse the sorting order.

Many of the other metadata options control what database must be used for the model and how the data is stored (these are really only useful if you need to map a model to an existing database).

### Methods

Minimally, in every model you should define the standard Python class method `__str__()` to return a human-readable string for each object. This string is used to represent individual records in the administration site (and anywhere else you need to refer to a model instance). Often this will return a title or name field from the model.

```
def __str__(self):
    return self.my_field_name
```

Another common method to include in Django models is get_absolute_url(), which returns a URL for displaying individual model records on the website (if you define this method then Django will automatically add a "View on Site" button to the model's record editing screens in the Admin site). A typical pattern for get_absolute_url() is shown below.

```
def get_absolute_url(self):
    """Returns the URL to access a particular instance of the model."""
    return reverse('model-detail-view', args=[str(self.id)])
```

The reverse() function above is able to "reverse" your URL mapper (in the above case named 'model-detail-view') in order to create a URL of the right format.

You can also define any other methods you like, and call them from your code or templates (provided that they don't take any parameters).

## Model management

### Create and modify records

To create a record you can define an instance of the model and then call save().

```
# Create a new record using the model's constructor.
record = MyModelName(my_field_name="Instance #1")

# Save the object into the database.
record.save()
```

> Note: If you haven't declared any field as a primary_key, the new record will be given one automatically, with the field name id. You could query this field after saving the above record, and it would have a value of 1.

You can access the fields in this new record using the dot syntax, and change the values. You have to call save() to store modified values to the database.

```
# Access model field values using Python attributes.
print(record.id) # should return 1 for the first record.
print(record.my_field_name) # should print 'Instance #1'

# Change record by modifying the fields, then calling save().
record.my_field_name = "New Instance Name"
record.save()
```

### Search for records

You can search for records that match certain criteria using the model's objects attribute (provided by the base class).

We can get all records for a model as a QuerySet, using objects.all(). The QuerySet is an iterable object, meaning that it contains a number of objects that we can iterate/loop through.

```
all_books = Book.objects.all()

wild_books = Book.objects.filter(title__contains='wild')
number_wild_books = wild_books.count()
```

The fields to match and the type of match are defined in the filter parameter name, using the format: field_name__match_type (note the double underscore between title and contains above). Above we're filtering title with a case-sensitive match. There are many other types of matches you can do: icontains (case insensitive), iexact (case-insensitive exact match), exact (case-sensitive exact match) and in, gt (greater than), startswith, etc.

In some cases, you'll need to filter on a field that defines a one-to-many relationship to another model (e.g. a ForeignKey). In this case, you can "index" to fields within the related model with additional double underscores. So for example to filter for books with a specific genre pattern, you will have to index to the name through the genre field, as shown below:

```
# Will match on: Fiction, Science fiction, non-fiction etc.
books_containing_genre = Book.objects.filter(genre__name__icontains='fiction')
```

> Note: You can use underscores (__) to navigate as many levels of relationships (ForeignKey/ManyToManyField) as you like. For example, a Book that had different types, defined using a further "cover" relationship might have a parameter name: type__cover__name__exact='hard'.

## Define the LocalLibrary Models

### Genre model

```
from django.urls import reverse # Used in get_absolute_url() to get URL for specified ID

from django.db.models import UniqueConstraint # Constrains fields to unique values
from django.db.models.functions import Lower # Returns lower cased value of field

class Genre(models.Model):
    """Model representing a book genre."""
    name = models.CharField(
        max_length=200,
        unique=True,
        help_text="Enter a book genre (e.g. Science Fiction, French Poetry etc.)"
    )

    def __str__(self):
        """String for representing the Model object."""
        return self.name

    def get_absolute_url(self):
        """Returns the url to access a particular genre instance."""
        return reverse('genre-detail', args=[str(self.id)])

    class Meta:
        constraints = [
            UniqueConstraint(
                Lower('name'),
                name='genre_name_case_insensitive_unique',
                violation_error_message = "Genre already exists (case insensitive match)"
            ),
        ]
```

After the field, we declare a ` __str__()` method, which returns the name of the genre defined by a particular record. No verbose name has been defined, so the field label will be Name when it is used in forms. Then we declare the get_absolute_url() method, which returns a URL that can be used to access a detail record for this model (for this to work, we will have to define a URL mapping that has the name genre-detail, and define an associated view and template).

> verbose_name is a human-readable name for the field. If the verbose name isn't given, Django will automatically create it using the field's attribute name, converting underscores to spaces.

Setting unique=True on the field above prevents genres being created with exactly the same name, but not variations such as "fantasy", "Fantasy", or even "FaNtAsY". The last part of the model definition uses a constraints option on the model's metadata to specify that the lower case of the value in the name field must be unique in the database, and display the violation_error_message string if it isn't. Here we don't need to do anything else, but you can define multiple constrainst a field or fields. 

### Book model

The model uses a CharField to represent the book's title and isbn. For isbn, note how the first unnamed parameter explicitly sets the label as "ISBN" (otherwise, it would default to "Isbn"). 

```
class Book(models.Model):
    """Model representing a book (but not a specific copy of a book)."""
    title = models.CharField(max_length=200)
    author = models.ForeignKey('Author', on_delete=models.RESTRICT, null=True)
    # Foreign Key used because book can only have one author, but authors can have multiple books.
    # Author as a string rather than object because it hasn't been declared yet in file.

    summary = models.TextField(
        max_length=1000, help_text="Enter a brief description of the book")
    isbn = models.CharField('ISBN', max_length=13,
                            unique=True,
                            help_text='13 Character <a href="https://www.isbn-international.org/content/what-isbn'
                                      '">ISBN number</a>')

    # ManyToManyField used because genre can contain many books. Books can cover many genres.
    # Genre class has already been defined so we can specify the object above.
    genre = models.ManyToManyField(
        Genre, help_text="Select a genre for this book")

    def __str__(self):
        """String for representing the Model object."""
        return self.title

    def get_absolute_url(self):
        """Returns the URL to access a detail record for this book."""
        return reverse('book-detail', args=[str(self.id)])
```

The other parameters of interest in the author field are null=True, which allows the database to store a Null value if no author is selected, and on_delete=models.RESTRICT, which will prevent the book's associated author being deleted if it is referenced by any book.

> Warning: By default on_delete=models.CASCADE, which means that if the author was deleted, this book would be deleted too! We use RESTRICT here, but we could also use PROTECT to prevent the author being deleted while any book uses it or SET_NULL to set the book's author to Null if the record is deleted.

### BookInstance model

```
import uuid # Required for unique book instances

class BookInstance(models.Model):

    """Model representing a specific copy of a book (i.e. that can be borrowed from the library)."""
    id = models.UUIDField(primary_key=True, default=uuid.uuid4,
                          help_text="Unique ID for this particular book across whole library")
    book = models.ForeignKey('Book', on_delete=models.RESTRICT, null=True)
    imprint = models.CharField(max_length=200)
    due_back = models.DateField(null=True, blank=True)

    LOAN_STATUS = (
        ('m', 'Maintenance'),
        ('o', 'On loan'),
        ('a', 'Available'),
        ('r', 'Reserved'),
    )

    status = models.CharField(
        max_length=1,
        choices=LOAN_STATUS,
        blank=True,
        default='m',
        help_text='Book availability',
    )

    class Meta:
        ordering = ['due_back']

    def __str__(self):
        """String for representing the Model object."""
        return f'{self.id} ({self.book.title})'
```

* UUIDField is used for the id field to set it as the primary_key for this model. This type of field allocates a globally unique value for each instance (one for every book you can find in the library).

* DateField is used for the due_back date (at which the book is expected to become available after being borrowed or in maintenance). This value can be blank or null (needed for when the book is available). The model metadata (Class Meta) uses this field to order records when they are returned in a query.

* status is a CharField that defines a choice/selection list. As you can see, we define a tuple containing tuples of key-value pairs and pass it to the choices argument. The value in a key/value pair is a display value that a user can select, while the keys are the values that are actually saved if the option is selected. We've also set a default value of 'm' (maintenance) as books will initially be created unavailable before they are stocked on the shelves.

### Author model

```
class Author(models.Model):
    """Model representing an author."""
    first_name = models.CharField(max_length=100)
    last_name = models.CharField(max_length=100)
    date_of_birth = models.DateField(null=True, blank=True)
    date_of_death = models.DateField('Died', null=True, blank=True)

    class Meta:
        ordering = ['last_name', 'first_name']

    def get_absolute_url(self):
        """Returns the URL to access a particular author instance."""
        return reverse('author-detail', args=[str(self.id)])

    def __str__(self):
        """String for representing the Model object."""
        return f'{self.last_name}, {self.first_name}'
```

## Rerun the database migrations

All your models have now been created. Now rerun your database migrations to add them to your database.

```
py -3 manage.py makemigrations
py -3 manage.py migrate
```

## Django admin site

The Django admin application can use your models to automatically build a site area that you can use to create, view, update, and delete records. This can save you a lot of time during development, making it very easy to test your models and get a feel for whether you have the right data. The admin application can also be useful for managing data in production, depending on the type of website. The Django project recommends it only for internal data management (i.e. just for use by admins, or people internal to your organization), as the model-centric approach is not necessarily the best possible interface for all users, and exposes a lot of unnecessary detail about the models.

All the configuration required to include the admin application in your website was done automatically when you created the skeleton project. As a result, all you must do to add your models to the admin application is to register them.

### Register models

First, open admin.py in the catalog application (/django-locallibrary-tutorial/catalog/admin.py). It currently looks like this — note that it already imports django.contrib.admin:

Register the models by copying the following text into the bottom of the file. This code imports the models and then calls admin.site.register to register each of them.

```
from .models import Author, Genre, Book, BookInstance, Language

admin.site.register(Book)
admin.site.register(Author)
admin.site.register(Genre)
admin.site.register(BookInstance)
admin.site.register(Language)
```

### Create a superuser
In order to log into the admin site, we need a user account with Staff status enabled. In order to view and create records we also need this user to have permissions to manage all our objects. You can create a "superuser" account that has full access to the site and all needed permissions using manage.py.

Call the following command, in the same directory as manage.py, to create the superuser. You will be prompted to enter a username, email address, and strong password.

```
py -3 manage.py createsuperuser
```

Once this command completes a new superuser will have been added to the database. Now restart the development server so we can test the login:

```
python3 manage.py runserver
```

### Log in and use the site

To login to the site, open the /admin URL (e.g. http://127.0.0.1:8000/admin) and enter your new superuser userid and password credentials (you'll be redirected to the login page, and then back to the /admin URL after you've entered your details).

This part of the site displays all our models, grouped by installed application. You can click on a model name to go to a screen that lists all its associated records, and you can further click on those records to edit them. You can also directly click the Add link next to each model to start creating a record of that type.


## Advanced configuration

You can further customize the interface to make it even easier to use. Some of the things you can do are:

* List views:

  * Add additional fields/information displayed for each record.
  * Add filters to select which records are listed, based on date or some other selection value (e.g. Book loan status).
  * Add additional options to the actions menu in list views and choose where this menu is displayed on the form.

* Detail views

  * Choose which fields to display (or exclude), along with their order, grouping, whether they are editable, the widget used, orientation etc.
  * Add related fields to a record to allow inline editing (e.g. add the ability to add and edit book records while you're creating their author record).

### Register a ModelAdmin class

To change how a model is displayed in the admin interface you define a ModelAdmin class (which describes the layout) and register it with the model.

Let's start with the Author model. Open admin.py in the catalog application (/django-locallibrary-tutorial/catalog/admin.py). Comment out your original registration (prefix it with a #) for the Author model:

Now add a new AuthorAdmin and registration as shown below.

```
# Define the admin class
class AuthorAdmin(admin.ModelAdmin):
    pass

# Register the admin class with the associated model
admin.site.register(Author, AuthorAdmin)
```

Now to create and register the new models; for the purpose of this demonstration, we'll instead use the @register decorator to register the models (this does exactly the same thing as the admin.site.register() syntax):

```
# Register the Admin classes for Book using the decorator
@admin.register(Book)
class BookAdmin(admin.ModelAdmin):
    pass

# Register the Admin classes for BookInstance using the decorator
@admin.register(BookInstance)
class BookInstanceAdmin(admin.ModelAdmin):
    pass
```    

### Configure list views

The LocalLibrary currently lists all authors using the object name generated from the model `__str__()` method. This is fine when you only have a few authors, but once you have many you may end up having duplicates. To differentiate them, or just because you want to show more interesting information about each author, you can use list_display to add additional fields to the view.

Replace your AuthorAdmin class with the code below. The field names to be displayed in the list are declared in a tuple in the required order, as shown (these are the same names as specified in your original model).

```
class AuthorAdmin(admin.ModelAdmin):
    list_display = ('last_name', 'first_name', 'date_of_birth', 'date_of_death')
```    

For our Book model we'll additionally display the author and genre. The author is a ForeignKey field (one-to-many) relationship, and so will be represented by the `__str__()` value for the associated record. Replace the BookAdmin class with the version below.

```
class BookAdmin(admin.ModelAdmin):
    list_display = ('title', 'author', 'display_genre')
```

Unfortunately we can't directly specify the genre field in list_display because it is a ManyToManyField (Django prevents this because there would be a large database access "cost" in doing so). Instead we'll define a display_genre function to get the information as a string (this is the function we've called above; we'll define it below).

> Note: Getting the genre may not be a good idea here, because of the "cost" of the database operation. We're showing you how because calling functions in your models can be very useful for other reasons — for example to add a Delete link next to every item in the list.

Add the following code into your Book model (models.py). This creates a string from the first three values of the genre field (if they exist) and creates a short_description that can be used in the admin site for this method.

```
def display_genre(self):
    """Create a string for the Genre. This is required to display genre in Admin."""
    return ', '.join(genre.name for genre in self.genre.all()[:3])

display_genre.short_description = 'Genre'
```

### Add list filters

Once you've got a lot of items in a list, it can be useful to be able to filter which items are displayed. This is done by listing fields in the list_filter attribute. Replace your current BookInstanceAdmin class with the code fragment below.

```
class BookInstanceAdmin(admin.ModelAdmin):
    list_filter = ('status', 'due_back')
```

### Organize detail view layout

By default, the detail views lay out all fields vertically, in their order of declaration in the model. You can change the order of declaration, which fields are displayed (or excluded), whether sections are used to organize the information, whether fields are displayed horizontally or vertically, and even what edit widgets are used in the admin forms.

#### Controlling which fields are displayed and laid out

Update your AuthorAdmin class to add the fields line, as shown below:

```
class AuthorAdmin(admin.ModelAdmin):
    list_display = ('last_name', 'first_name', 'date_of_birth', 'date_of_death')

    fields = ['first_name', 'last_name', ('date_of_birth', 'date_of_death')]
```

The fields attribute lists just those fields that are to be displayed on the form, in order. Fields are displayed vertically by default, but will display horizontally if you further group them in a tuple (as shown in the "date" fields above).

#### Sectioning the detail view

You can add "sections" to group related model information within the detail form, using the fieldsets attribute.

In the BookInstance model we have information related to what the book is (i.e. name, imprint, and id) and when it will be available (status, due_back). We can add these to our BookInstanceAdmin class as shown below, using the fieldsets property.

```
@admin.register(BookInstance)
class BookInstanceAdmin(admin.ModelAdmin):
    list_filter = ('status', 'due_back')

    fieldsets = (
        (None, {
            'fields': ('book', 'imprint', 'id')
        }),
        ('Availability', {
            'fields': ('status', 'due_back')
        }),
    )
```

Each section has its own title (or None, if you don't want a title) and an associated tuple of fields in a dictionary — the format is complicated to describe, but fairly easy to understand if you look at the code fragment immediately above.

#### Inline editing of associated records

Sometimes it can make sense to be able to add associated records at the same time. For example, it may make sense to have both the book information and information about the specific copies you've got on the same detail page.

You can do this by declaring inlines, of type TabularInline (horizontal layout) or StackedInline (vertical layout, just like the default model layout). You can add the BookInstance information inline to our Book detail by specifying inlines in your BookAdmin:

```
class BooksInstanceInline(admin.TabularInline):
    model = BookInstance

@admin.register(Book)
class BookAdmin(admin.ModelAdmin):
    list_display = ('title', 'author', 'display_genre')

    inlines = [BooksInstanceInline]
```    

## Create our home page

### Create the index page

The first page we'll create is the index page (catalog/). The index page will include some static HTML, along with generated "counts" of different records in the database. To make this work we'll create a URL mapping, a view, and a template.

#### URL mapping

When we created the skeleton website, we updated the locallibrary/urls.py file to ensure that whenever a URL that starts with catalog/ is received, the URLConf module catalog.urls will process the remaining substring.

The following code snippet from locallibrary/urls.py includes the catalog.urls module:

```
urlpatterns += [
    path('catalog/', include('catalog.urls')),
]
```


