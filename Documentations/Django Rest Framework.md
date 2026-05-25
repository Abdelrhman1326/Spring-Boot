## The Serializer (Data Validator & Processor)

```python
from rest_framework import serializers
from django.contrib.auth.models import User
from django.contrib.auth.password_validation import validate_password

class RegisterSerializer(serializers.ModelSerializer):
    email = serializers.EmailField(required=True)
    password = serializers.CharField(write_only=True, required=True, validators=[validate_password])

    class Meta:
        model = User
        fields = ['username', 'email', 'password']

    def create(self, validated_data):
        user = User.objects.create_user(**validated_data)
        return user
```

**What this is:** A "form processor" that handles user registration data.

**Think of it like:** A bouncer at a club who checks IDs and processes entry forms.

### Breaking it down:

**The imports:**

- `serializers` = Tools for validating and converting data
- `User` = Django's built-in user model (like a template for storing user info)
- `validate_password` = Django's password strength checker

**The class definition:**
```python
class RegisterSerializer(serializers.ModelSerializer):
```

**What this means:** "I'm creating a special form processor that works with Django models"

**The field definitions:**
```python
email = serializers.EmailField(required=True)
password = serializers.CharField(write_only=True, required=True, validators=[validate_password])
```

**Think of it like:** Setting up rules for form fields:

- `EmailField(required=True)` = "This must be a valid email address, and it's mandatory"
- `CharField(write_only=True)` = "This is text, but once submitted, don't show it back to anyone" (security for passwords)
- `validators=[validate_password]` = "Check that this password is strong enough"

**The Meta class:**
```python
class Meta:
    model = User
    fields = ['username', 'email', 'password']
```

**What this means:** "This form is connected to the User model, and I only want to handle these 3 fields"

**The create method:**
```python
def create(self, validated_data):
    user = User.objects.create_user(
        username=validated_data['username'],
        email=validated_data['email'],
        password=validated_data['password']
    )
    return user
```

**What this does:** "After validating the data, create a new user in the database"

- `validated_data` = The clean, approved data that passed all checks
- `create_user()` = Django's special method that properly handles password encryption
- 
#### Feeling a little confused? Let's clarify how it works:

When a request with a JSON payload is received in a Django view, we need to deserialize it. Deserialization is the process of converting JSON data into a Python dictionary.

so the view will look like this:
``` python
class ExampleView(APIView):
	def post(self, request, *args, **kwargs):
		# initialize a serializer and pass the request data to get serialized:
		serializer = ExampleSerializer(data=request.data)
		
		# .is_valid() -> runs the validation methods that are found in the serializer
		if serializer.is_valid():
			instance = serializer.save()
			
			return Response(serializer.data, status=status.HTTP_201_CREATED)
			
		# validation failed:
		return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

`serializer.validated_data` vs. `serializer.data`:

| Feature         | serializer.validated_data                                                                                                        | `serializer.data`                                                                                                               |
| --------------- | -------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| Purpose         | To retrieve the **clean, validated input data** for **internal processing** (e.g., creating/updating a database record).         | To retrieve the **output data** (the representation) for **external use** (e.g., sending back in an HTTP response).             |
| **Data Flow**   | **Input** (from `request.data`)                                                                                                  | **Output** (what the serializer is ready to _render_).                                                                          |
| Content         | Only fields passed to the serializer and successfully validated. Missing fields are excluded. **Read-only fields are excluded.** | All fields defined on the serializer, including fields with default values, **read-only fields**, and data from `get_` methods. |
| **Access Time** | Available **after** `serializer.is_valid()` is called.                                                                           | Available **after** instantiation (if passing an object) **OR** after `serializer.save()` (if creating/updating).               |
| **Type**        | A standard Python **dictionary (dict)**.                                                                                         | A standard Python **dictionary (dict)** (but its contents reflect the _output_ structure).                                      |

## The Auto vs. Manual Model Interaction

In Django REST Framework (DRF), the core difference in object persistence lies in whether the serializer itself provides the save logic.

### 1. `serializers.Serializer` (Manual Persistence)

If your serializer inherits from **`serializers.Serializer`**, you must **manually write** the persistence logic.

- **View's Role:** The view **must call** `serializer.save()`. This is the required entry point to trigger saving.
    
- **Serializer's Role:** You **must define** both the `create(self, validated_data)` and `update(self, instance, validated_data)` methods within the serializer. These methods contain the explicit Django ORM calls (`Model.objects.create(...)` or `instance.save()`) to handle the database interaction.
    
- **You **never** call `.create()` or `.update()` directly from the view; the view calls `.save()`, which then executes the appropriate method you defined.
    

### 2. `serializers.ModelSerializer` (Automatic Persistence)

If your serializer inherits from **`serializers.ModelSerializer`**, the save logic is handled automatically.

- **View's Role:** The view **must still call** `serializer.save()`. This is required to initiate the saving process after validation.
    
- **Serializer's Role:** You **do not** need to write the `create()` or `update()` methods. The base `ModelSerializer` provides these methods by default, using the `Meta` model to automatically handle the Django ORM interaction.
    
- **Correction:** The "automatic" part means the logic is built-in, but the view still needs to **trigger** that logic by calling `.save()`.
    

In summary: **`.save()` is always called in the view to trigger persistence, regardless of the serializer type.** The difference is whether you have to _write_ the internal `create`/`update` methods yourself.

## The URLs (Address Routing)

```python
from django.urls import path
from .views import RegisterView

urlpatterns = [
    path('register/', RegisterView.as_view(), name='register'),
]
```

**What this is:** A local address book for your users app.

**Think of it like:** Signs in a building that say "Registration Office → Room 101"

### Breaking it down:

- `path('register/', ...)` = "When someone visits `/register/`, do this..."
- `RegisterView.as_view()` = "...send them to the RegisterView class"
- `name='register'` = "Give this route a nickname so we can reference it elsewhere"

**Real-world example:** If your website is `mysite.com`, this creates the URL `mysite.com/api/register/`

## The Main URL Configuration

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('users.urls')),
]
```

**What this is:** The main address book for your entire project.

**Think of it like:** The main directory at a shopping mall that points to different sections.

### Breaking it down:

**The imports:**

- `admin` = Django's built-in admin interface
- `path, include` = Tools for creating and connecting routes

**The URL patterns:**
```python
path('admin/', admin.site.urls)
```

**What this means:** "When someone goes to `/admin/`, show them Django's admin panel"

```python
path('api/', include('users.urls'))
```

**What this means:** "When someone goes to `/api/`, look at the users app's URL file for what to do next"


## The Magic Happens in the Serializer's `save()` Method

When you don't see explicit database saving code, Django is doing it automatically behind the scenes. Here's how:

### What You Don't See (But Django Does Automatically)

When your view calls the serializer, this is what happens invisibly:
```python
# In your view (simplified version):
serializer = RegisterSerializer(data=request.data)
if serializer.is_valid():
    serializer.save()  # ← This is where the magic happens!
```

## The `save()` Method Chain

When you call `serializer.save()`, Django automatically:

1. **Calls your `create()` method** (the one you wrote in the serializer)
2. **Your `create()` method calls `User.objects.create_user()`**
3. **`create_user()` automatically saves to the database**

**Think of it like:** You press a button on a vending machine, and it automatically:

- Takes your money
- Selects the item
- Drops it in the slot
- Gives you change

You only see the button press, but many steps happen automatically.

### Let's Trace the Full Flow

Here's what happens step by step:

```python
# 1. User sends registration data
# 2. View receives data and creates serializer
serializer = RegisterSerializer(data=request.data)

# 3. View validates data
if serializer.is_valid():
    
    # 4. View calls save() - THIS TRIGGERS THE DATABASE SAVE
    user = serializer.save()
    
    # At this point, user is already saved to database!
```

### Inside Your `create()` Method

```python
def create(self, validated_data):
    user = User.objects.create_user(  # ← This line saves to database!
        username=validated_data['username'],
        email=validated_data['email'],
        password=validated_data['password']
    )
    return user  # Returns the saved user object
```

**Key point:** `User.objects.create_user()` is a Django method that automatically:

- Creates a new user object
- Encrypts the password
- **Saves it to the database immediately**
- Returns the saved user object

## Why This Automatic Saving Happens

Django follows a pattern called "Active Record" where:

- Creating an object = Saving to database
- Updating an object = Saving changes to database
- Deleting an object = Removing from database

**Think of it like:** When you write in a Google Doc, it automatically saves. You don't need to press Ctrl+S every time.

## Manual vs Automatic Saving

### Automatic (what you're using):

```python
user = User.objects.create_user(...)  # Creates AND saves
```

### Manual (if you wanted explicit control):

```python
user = User(username=..., email=...)  # Creates but doesn't save
user.set_password(password)           # Sets password
user.save()                           # NOW it saves to database
```

## The Complete Picture

```python
# Your serializer
def create(self, validated_data):
    user = User.objects.create_user(...)  # Database save happens here
    return user

# In your view (somewhere)
serializer = RegisterSerializer(data=request.data)
if serializer.is_valid():
    user = serializer.save()  # This calls your create() method
    # User is now in the database!
```

**Think of it like:**

- You write a letter (`create()` method)
- You put it in a mailbox (`serializer.save()`)
- The postal service automatically picks it up and delivers it (Django saves to database)

### Other Django Methods That Auto-Save

- `Model.objects.create()` - Creates and saves
- `Model.objects.get_or_create()` - Gets existing or creates and saves
- `Model.objects.update_or_create()` - Updates existing or creates and saves
- `model_instance.save()` - Saves changes to existing object
#### Summary

Django stores data automatically because:

1. You call `serializer.save()`
2. This calls your `create()` method
3. Your `create()` method uses `User.objects.create_user()`
4. `create_user()` automatically saves to the database

You don't need to explicitly tell Django to save because the methods you're using (`create_user()`) are designed to save automatically. It's like having a smart assistant who knows to file papers immediately after you write them.

#### DeSerialization:

hmm got it, so the problem is if i have data in JSON form, the serializer that creates an object for me isn't expecting the data from me in a JSON form, it is expecting it in python dict form, so i have to convert it: 
```python
# create new instance to pass to the serializer:
snippet = Snippet(code='foo = "bar"\n')
snippet.save()
serializer = SnippetSerializer(snippet)
	snippet

# now we have created an Snippet instace "Snippet object" and then passed it to the Serializer to validate the data and save it to the db.
data = serializer.data # this will just return back the same Object again and save it to data variable

# what if we need to convert that data to json form:
json_data = JSONRenderer.render(data) # -> now this data is in json form

# what if we need to convert that json data to it's original form:
import io
stream = io.BytesIO(json_data) # stream will stream the data in a way that python can understand

# now we can convert the data to python dict back using that stream:
python_dict = JSONParser().parse(stream)
```

#### Complete Example to Show Both:
```python
# SERIALIZATION - converting object to data
snippet = Snippet(code='foo = "bar"\n')
snippet.save()  # Save the instance to DB first
serializer = SnippetSerializer(snippet)  # Serialize existing object
data = serializer.data

# DESERIALIZATION - converting data to object  
new_serializer = SnippetSerializer(data=data)  # Note: data= parameter
if new_serializer.is_valid():
    new_snippet = new_serializer.save()  # This creates a new DB record
```

So in your original example, you were doing **serialization**, which means you need `snippet.save()` to save the model instance to the database before serializing it.

`serializer.save()` is for **deserialization** when you're creating objects from incoming data.

## Ingesting Data:

`save method:` In both serializer and model serializer, using save method, will either update an object in the db. if it already exist, so it will auto call the .update method, and if the object doesn't exist it will be created where the save method will auto call the .create method.
Save method is never called automatically, you have to call it when you add data to the db.

### Serializer vs ModelSerializer:

`Serializer:` Purely manual where you have to define every field manually; writing your own .create() and .update() methods, and defining your own custom fields

example:
```python
from rest_framework import serializers
from django.core.validators import RegexValidator
from django.contrib.auth.models import User
from django.core.exceptions import ValidationError

class UserSerializer(serializers.Serializer):
    id = serializers.IntegerField(read_only=True)
    
    username = serializers.CharField(
        max_length=150,
        validators=[
            RegexValidator(
                regex=r'^[\w.@+-]+$',
                message='Username may contain letters, digits and @/./+/-/_ only.'
            )
        ]
    )
    
    email = serializers.EmailField()

    def validate_username(self, value):
        if User.objects.filter(username=value).exists():
            raise serializers.ValidationError("This username is already taken.")
        return value

    def validate_email(self, value):
        if User.objects.filter(email=value).exists():
            raise serializers.ValidationError("This email is already registered.")
        return value

    def create(self, validated_data):
        return User.objects.create(**validated_data)

    def update(self, instance, validated_data):
        instance.username = validated_data.get('username', instance.username)
        instance.email = validated_data.get('email', instance.email)
        instance.save()
        return instance
```

`ModelSerializer:`
```python
from rest_framework import serializers
from .models import User

class UserSerializer(serializers.ModelSerializer):
	class Meta:
		model = User
		fields = ['id', 'username', 'email']

	def validate_username(self, value):
        if User.objects.filter(username=value).exists():
            raise serializers.ValidationError("This username is already taken.")
        return value

    def validate_email(self, value):
        if User.objects.filter(email=value).exists():
            raise serializers.ValidationError("This email is already registered.")
        return value
```

`note:` now we are not defining the details for each field in the serializer, and we are just including the wanted data variables in the fields list, but to do this, these included variables must be included in the model that we are using.

## Generics:

### 1. `CreateAPIView` (for auto `POST`):

#CreateAPIView
The view:
``` python
queryset = Product.objects.all()
	serializer_class = ProductSerializer
```

`The serializer is the same for both:`
``` python
class ProductSerializer(serializers.ModelSerializer):
	class Meta:
		model = Product
		fields = ['id', 'name', 'price', 'in_stock']
```

#querysets 
`you can omit the queryset most of the time, in cases like in the case of making a createAPIView, you actually don't need the queryset in that case, you are just creating new objects and adding them to the db, you are not getting old ones, but in case like of making a RetrieveAPIView, you have to provide the queryset, cause you are using the pk or the id passed in the request's url, which will be used to select an object from the queryset and return it in the response body.`

##### In CreateAPIView we can make a perform_create method, this will be auto called as long as we are doing that in in a CreateAPIView, the method takes self and a serializer.
#example
``` python
from rest_framework.generics import CreateAPIView
from .models import Product
from .serializers import ProductSerializer

class ProductCreate(CreateAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer

    def perform_create(self, serializer):
        title = serializer.validated_data.get('title')
        price = serializer.validated_data.get('price')
        print(f"title: {title}, price: {price}")
        serializer.save()
```

### 2. RetrieveAPIView:
`used to auto get data from the endpoint by passing a pk or an id of an object in the db`
``` python
class ProductRetrieveView(generics.RetrieveAPIView):
	queryset = Product.objects.all()
	serializer_class = ProductSerializer
```

### 3. ListAPIView:
``ListAPIView` is a class-based view in Django REST Framework used to **retrieve and return a list of objects** (typically as JSON). It maps to the **HTTP GET** method **without a primary key (pk)** — meaning: `GET /products/` (not `/products/1/`).`
``` python
from rest_framework.generics import ListAPIView
from .models import Product
from .serializers import ProductSerializer

class ProductListView(ListAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
```
Calling `GET /api/products/` will return:
``` json
[
  { "id": 1, "title": "Book", "price": 10 },
  { "id": 2, "title": "Pen", "price": 2 }
]
```
#### We can also make a list_create_api_view, this will work as a listAPIView when we use a get request with it, and we can.
``` python
from rest_framework import generics
from .models import Product
from .serializers import ProductSerializer

class ProductListCreateAPIView(generics.ListCreateAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer

```
#### we can also play around with the imports and do something like that:
``` python
from rest_framework.generics import ListCreateAPIView
from .models import Product
from .serializers import ProductSerializer

class ProductListCreateAPIView(ListCreateAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
```

#### args and kwargs:
in a Django view function, we define both args which are positional arguments and kwargs which are key arguments
``` python
def product_alt_view(request, *args, **kwargs):
	# we can extract a positional argument like this:
	if args:
		title1 = args[0]
	# and we can extract a key argument like that:
	if kwargs:
		title2 = kwargs.get('title_2')
```

### 4. UpdateAPIView And DestroyAPIView:
#### UpdateView:
``` python
class ProductUpdateView(generics.UpdateAPIView):
	queryset = Product.objects.all()
	serializer_class = ProductSerializer
```
This way will auto take the object id that is passed in the PATCH request URL "args", edit the object, then auto save it to the DB "patch it".

We are also still able to play around with the object before getting it patched in the DB in the following way:
``` python
class ProductUpdateView(generics.UpdateAPIView):
	queryset = Product.objects.all()
	serializer_class = ProductSerializer

	def perform_update(self, serializer):
		# make the instance, save it then assign it to the variable
		instance = serializer.save()
```

#### DestroyView:
``` python
class ProductDestroyView(generics.DestroyAPIView):
	queryset = Product.objects.all()
	serializer_class = ProductSerializer
```

## Mixins and Generic API View:

``` python
from rest_framework import mixins, generics
from .models import Product
from .serializers import ProductSerializer

class ProductMixinView(
	    mixins.CreateModelMixin,
	    mixins.ListModelMixin,
	    mixins.RetrieveModelMixin,
	    generics.GenericAPIView
	):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
    lookup_field = 'pk'

    def get(self, request, *args, **kwargs):
        if self.lookup_field in kwargs:
            return self.retrieve(request, *args, **kwargs)
        return self.list(request, *args, **kwargs)

    def post(self, request, *args, **kwargs):
        return self.create(request, *args, **kwargs)
```


## Session Authentication and Permissions:

#### Permission classes:
If we want to allow a user to do something only when he is authenticated, we can do as follows:
``` python
from rest_framework import generics, mixins, permissions, authentication

class ProductCreateView(generics.CreateAPIView):
	queryset = Product.objects.all()
	serializer_class = ProductSerializer
	authentication_classes = [authentication.SessionAuthentication]
	permission_classes = [permissions.IsAuthenticated]
```

#### 1. **`SessionAuthentication`** → _Authentication_

- Purpose: **Identify** the user making the request.
    
- It checks the session cookie, finds the logged-in user in the database, and attaches that user object to `request.user`.
    
- After this step, DRF _knows who you are_, but it hasn't yet decided if you're allowed to do anything.
    
---

#### 2. **`permissions.IsAuthenticated`** → _Authorization_

- Purpose: **Decide** whether the identified user is allowed to access this view.
---
### Why both are needed

If you only use:

- **`SessionAuthentication`** → DRF will know the user if logged in, but _anonymous users can still hit the view_ unless you explicitly block them.
    
- **`permissions.IsAuthenticated`** → DRF will _block_ anyone who’s not logged in, regardless of how they authenticated (session, token, etc.).
    
---

🔹 **Think of it like this:**

- Authentication = _"Who are you?"_ ✅
    
- Permissions = _"Now that I know who you are, can you enter this room?"_ 🚫✅

## User and Group Permissions with DjangoModelPermissions:

### 1. Built-in Django's model permissions:
##### Our scenario: we want a user to be able to add "publish" books to the DB, but not delete any book, here we can simply use the built in permissions:
``` python
from rest_framework import permissions, viewsets
from .models import Book
from .serializers import BookSerializer

class BookViewSet(viewsets.ModelViewSet):
	queryset = Book.objects.all()
	serializer_class = BookSerializer
	permission_classes = [
		permissions.IsAuthenticated,
		permissions.DjangoModelPermissions
	]
```
But how does Django know that a user is only allowed to view and create books, but never delete or patch them?
1. **Assign `view_book` and `add_book` permissions** to users/groups. 
2. **Do not** give `delete_book` or `change_book` permissions.

### Code to create groups and assign permissions

``` python
from django.contrib.auth.models import Group, Permission
from django.contrib.contenttypes.models import ContentType
from myapp.models import Book

# Create groups
publishers_group, _ = Group.objects.get_or_create(name='Publishers')
moderators_group, _ = Group.objects.get_or_create(name='Moderators')

# Get Book content type
book_ct = ContentType.objects.get_for_model(Book)

# Get permissions
add_book = Permission.objects.get(codename='add_book', content_type=book_ct)
view_book = Permission.objects.get(codename='view_book', content_type=book_ct)
delete_book = Permission.objects.get(codename='delete_book', content_type=book_ct)

# Assign permissions to groups
publishers_group.permissions.add(add_book, view_book)      # View + create only
moderators_group.permissions.add(view_book, delete_book)   # View + delete only
```
#note
This code is written in Django's manage.py shell, or we can do it with the GUI in the admin site.
#### Example usage:
``` python
from django.contrib.auth.models import Group
from rest_framework import serializers, views, status
from rest_framework.response import Response
from rest_framework.permissions import IsAuthenticated

ADMIN_SECRET_KEY = "supersecretkey123"  # store in env var in real apps

class BecomeAdminSerializer(serializers.Serializer):
    secret_key = serializers.CharField()

class BecomeAdminView(views.APIView):
    permission_classes = [IsAuthenticated]

    def post(self, request):
        serializer = BecomeAdminSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)

        if serializer.validated_data['secret_key'] != ADMIN_SECRET_KEY:
            return Response({"error": "Invalid secret key"}, status=status.HTTP_403_FORBIDDEN)

        admin_group, _ = Group.objects.get_or_create(name="Admins")
        request.user.groups.add(admin_group)

        return Response({"message": "You are now an admin!"})
```


### 2. Custom permission classes:

#### We add custom permission classes in permissions.py file:
``` python
from rest_framework.permissions import BasePermission

class IsOwnerOrReadOnly(BasePermission):
    """
    Allow everyone to view, but only the owner can edit/delete.
    """

    def has_object_permission(self, request, view, obj):
        # Safe methods are always allowed (GET, HEAD, OPTIONS)
        if request.method in ('GET', 'HEAD', 'OPTIONS'):
            return True
        # Write permissions only for owner
        return obj.owner == request.user
```
#### And then we can use it:
``` python
class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    permission_classes = [IsOwnerOrReadOnly]
```
#note 
Here we are comparing complete objects not just ids:
``` python
return obj.owner == request.user
```
But here we are comparing owner "user" secondary key to other user primary key:
``` python
return obj.owner_id == request.user.id
```

## ViewSets and Routers:
### Step one: "Make a serializer":
``` python
from rest_framework import serializers
from .models import Item

class ItemSerializer(serializers.ModelSerializer):
	class Meta:
		model = Item
		fields = '__all__'
```

### Step Two: "Make the view":
``` python
class ItemViewSet(viewsets.ModelViewSet):
    queryset = Item.objects.all().order_by('-created')
    serializer_class = ItemSerializer
```
`ModelViewSet` automatically provides:
- `list` → GET `/items/`
- `retrieve` → GET `/items/{id}/`
- `create` → POST `/items/`
- `update` → PUT `/items/{id}/`
- `partial_update` → PATCH `/items/{id}/`
- `destroy` → DELETE `/items/{id}/`
### Step Three:
Create the Router:
``` python
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import ItemViewSet

router = DefaultRouter()
router.register(r'items', ItemViewSet)

urlpatterns = [
    path('', include(router.urls)),
]
```

- **`path`** → Function from Django used to define a URL pattern (e.g., `/items/`) and link it to some view or set of views.
- **`include`** → Lets you reference and “include” URL patterns from another file/module instead of writing them all in one file.  
    In our case, we’ll use it to include the URL patterns automatically generated by the router.
---
`from rest_framework.routers import DefaultRouter`

- Imports **`DefaultRouter`** from DRF.
- `DefaultRouter` is a helper that **automatically generates CRUD URL patterns** for ViewSets, plus an optional root API view (index page listing all registered endpoints).
- Without this, we’d have to manually write 6+ `path()` calls for each CRUD method.
---
`from .views import ItemViewSet`

- Imports our `ItemViewSet` from the **current app’s** `views.py`.
- `ItemViewSet` is a class that defines how to handle GET/POST/PUT/PATCH/DELETE for our `Item` model.
---
`router = DefaultRouter()`

- Creates an **instance** of `DefaultRouter`.
    
- Think of this as an “empty registry” that we’ll fill with viewsets.
    
- Once viewsets are registered, this `router` will know how to generate URLs for them.
---
`router.register(r'items', ItemViewSet)`

- Registers a viewset with the router.
- **First argument**: `'items'` — This is the **URL prefix**. It means all generated URLs will start with `/items/`.
- **Second argument**: `ItemViewSet` — The viewset class that will handle the CRUD operations.
- DRF now knows:
    - `/items/` → list + create
    - `/items/{id}/` → retrieve + update + partial update + delete
        
- `r'items'` → The `r''` just means it’s a raw string (avoids issues with backslashes, though here it doesn’t really matter).
---
`urlpatterns = [     path('', include(router.urls)), ]`

- `urlpatterns` is the **list of all URL patterns** for this app.
- `path('', include(router.urls))` →
    
    - The first `''` means these routes will be available exactly where this file’s URLs are included (no extra prefix here).
        
    - `include(router.urls)` takes all the URLs the router generated and plugs them into Django’s URL system.
        
- This is why you don’t see explicit definitions for `list`, `retrieve`, etc. — they’re already inside `router.urls`.

## Related Fields and Foreign key Serializer:
### Serializer Method Field:

**SerializerMethodField**  is a read only serializer field whose value is computed dynamically by calling a method on the serializer class instead of directly coming from a model field or attribute

It’s useful when:
- You want to include **calculated data** in your API response.
- You want to **format or process a value** before sending it.
- The data doesn’t exist directly on the model.

``` python
from rest_framework import serializers
from .models import Product

class ProductSerializer(serializer.ModelSerializer):
	final_price = serializers.SerializerMethodField()

	class Meta:
		model = Product
		fields = ['name', 'price', 'discount', 'final_price']

	def get_final_price(self, obj):
		return obj.price - (obj.price * (obj.discount / 100))
```

#### How to make some fields read only "avoid patch and post requests":
``` python
class UserSerializer(serializers.ModelSerializer):
	user_id = serializer.PrimaryKeyRelatedField(read_only=True)

	class Meta:
		model = User
		fields = ['id', 'user_id', 'username', 'email', 'password']
```
or we can do it in Meta class:
``` python
class MySerializer(serializers.ModelSerializer):
    class Meta:
        model = MyModel
        fields = ['id', 'name', 'user_id']
        read_only_fields = ['user_id']
```


## Relationships:

### many-to-one relationship:
#### 1) The model (what the DB looks like)
``` python
# models.py
from django.db import models
from django.contrib.auth.models import User

class Post(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    user = models.ForeignKey(User, on_delete=models.CASCADE)  # the owner
```
- `user` is a `ForeignKey` relationship to `User`.
- Django also gives the model attribute `post.user_id` (an integer PK) automatically.

#### 2) Default `ModelSerializer` behaviour (what DRF makes for you)
``` python
from rest_framework import serializers

class PostSerializer(serializers.ModelSerializer):
    class Meta:
        model = Post
        fields = ['id', 'title', 'content', 'user']
```
- **By default** DRF maps `user` (ForeignKey) to a **`PrimaryKeyRelatedField`** (read/write) internally.
- Output example for a post owned by user with id 5:
``` json
{ "id": 1, "title": "...", "content": "...", "user": 5 }
```
On **create/update**, if client sends `"user": 6`, DRF will try to resolve `6` to a `User` instance (so it is writable).
#### 3) Make the relationship read-only (prevent clients from changing owner)

``` python
class PostSerializer(serializers.ModelSerializer):
    class Meta:
        model = Post
        fields = ['id', 'title', 'content', 'user']
        read_only_fields = ['user']
```
- `user` will be **included in output** but **ignored** if client sends it in POST/PUT.
- This is the simplest way to protect the owner field.

Option B — explicitly override field:
``` python
class PostSerializer(serializers.ModelSerializer):
    user = serializers.PrimaryKeyRelatedField(read_only=True)

    class Meta:
        model = Post
        fields = ['id', 'title', 'content', 'user']
```

Trick: you use another name for the related field, for example we can use owner, instead of user, but because there is no an actual field named owner, we have to tell Django that we will use the name owner instead of user, "we are simply mapping them":
``` python
class PostSerializer(serializers.ModelSerializer):
	owner = serializers.PrimaryKeyRelatedField(source='user', read_only=True)
```


Now we have linked a post to a user "owner" which is a foreign key pointing to a User model instance, this is a many-to-one relationship, where we are linking many posts to one single user
``` scss
User (1) ───────< Post (many)
```

### one-to-one relationship:
``` scss
User (1) ────── Profile (1)
```
Imagine, every user instance should be linked to a profile class instance, where each user has his own and only profile, including extra info, like the bio, here we need to make a one-to-one relationship where every profile instance has a OneToOne field, which is a foreign key pointing to a specific User class instance:
``` python
class User(models.Model):
	username = models.CharField(max_length=100)

class Profile(models.Model):
	user = models.OneToOneField(User, on_delete=models.CASCADE)
	bio = TextField(max_length=2500)
```
##### Query Example:
``` python
# Create
user = User.objects.create(username="AbdElrhman")
profile = Profile.objects.create(user=user, bio="Software Engineer")

# Access
profile.user       # Get user from profile
user.profile       # Get profile from user
```

##### Remember: For any relationship we have to make the ForeignKeys read-only fields in the serializers to prevent data relations corruption caused by hacking attempts:
``` python
from rest_framework import serializers
from .models import Profile

class ProfileSerializer(serializers.ModelSerializer):
    user = serializers.ReadOnlyField()  # Can't be changed

    class Meta:
        model = Profile
        fields = ['id', 'name', 'price', 'user']
```

### one-to-many and many-to-one relationships:

In a **many-to-one** relationship, many objects of one type are related to a single object of another type. For example, many `Book` objects can be written by one `Author`. In the `Book` model, you would have a `ForeignKey` to the `Author` model.

In a **one-to-many** relationship, one object of one type is related to many objects of another type. For example, one `Author` can write many `Book` objects. Django automatically provides reverse access from the one side to the many side.

``` python
class Author(models.Model):
    name = models.CharField(max_length=100)
    # No need for "books" field here — Django gives you reverse access automatically when we use related_name="books"

class Book(models.Model):
    name = models.CharField(max_length=250)
    release_date = models.DateField()
    genres = models.TextField(max_length=1000)
    author = models.ForeignKey(Author, on_delete=models.CASCADE, related_name="books")
```

#####  Query Example:
``` python
# get an author:
author = Author.objects.get(id=1)
# get author's books:
books = author.books.all() # notice: books is the related name we 've choosen
```

#### Note:
`This does not literally happen in Django. Django doesn't store arrays in the database (though this is possible with PostgreSQL). Instead, Django simplifies the query process. Behind the scenes, it filters objects where the foreign key matches the primary key.`
``` python
# what Django does behind the scenes:
author = Author.objects.get(id=1)
Books.objects.filter(author=author)
```

## Pagination:

We’ll make a simple `Post` model with a `user` field for demonstration:
``` python
from django.db import models
from django.contrib.auth.models import User

class Post(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    title = models.CharField(max_length=200)
    content = models.TextField()

    def __str__(self):
        return self.title
```

serializers.py
We’ll serialize the posts, using the `owner` example you mentioned earlier:
``` python
from rest_framework import serializers
from .models import Post

class PostSerializer(serializers.ModelSerializer):
    owner = serializers.PrimaryKeyRelatedField(source='user', read_only=True)

    class Meta:
        model = Post
        fields = ['id', 'title', 'content', 'owner']
```

views.py
We’ll create a `ModelViewSet` that lists posts (pagination will be applied here):
``` python
from rest_framework import viewsets
from .models import Post
from .serializers import PostSerializer

class PostViewSet(viewsets.ModelViewSet):
    queryset = Post.objects.all().order_by('id')
    serializer_class = PostSerializer
```

urls.py
We’ll register the viewset with a router:
``` python
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import PostViewSet

router = DefaultRouter()
router.register(r'posts', PostViewSet)

urlpatterns = [
    path('', include(router.urls)),
]
```

settings.py
We’ll enable pagination here:
``` python
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 10  # number of items per page
}
```

**Example Request:**
``` bash
GET /posts/?page=2
```
**Example Response:**
``` json
{
    "count": 12,
    "next": "http://127.0.0.1:8000/posts/?page=3",
    "previous": "http://127.0.0.1:8000/posts/?page=1",
    "results": [
        {
            "id": 6,
            "title": "Post 6",
            "content": "Content here...",
            "owner": 1
        },
        {
            "id": 7,
            "title": "Post 7",
            "content": "Content here...",
            "owner": 2
        }
    ]
}

```
#### Limit and offset:
``` bash
GET /posts/?limit=5&offset=0   → first 5 items
GET /posts/?limit=5&offset=5   → items 6–10
GET /posts/?limit=10&offset=30 → items 31–40
```
### Example Response:
``` json
{
    "count": 100,
    "next": "http://127.0.0.1:8000/posts/?limit=5&offset=5",
    "previous": null,
    "results": [
        {
            "id": 1,
            "title": "Post 1",
            "content": "Content here...",
            "owner": 1
        },
        {
            "id": 2,
            "title": "Post 2",
            "content": "Content here...",
            "owner": 2
        }
    ]
}
```


## Django Based Search:
### Simple Q-based search:

Serializer:
``` python
# myapp/serializers.py
from rest_framework import serializers
from .models import Article

class ArticleSerializer(serializers.ModelSerializer):
    author_username = serializers.CharField(source='author.username', read_only=True)

    class Meta:
        model = Article
        fields = ['id', 'title', 'body', 'author_username', 'created']
```
View (function-based DRF view):
``` python
# myapp/views.py
from rest_framework.decorators import api_view
from rest_framework.response import Response
from django.db.models import Q
from .models import Article
from .serializers import ArticleSerializer

@api_view(['GET'])
def search(request):
    q = request.GET.get('q', '').strip()
    if not q:
        return Response({'count': 0, 'results': []})

    qs = Article.objects.filter(
        Q(title__icontains=q) |
        Q(body__icontains=q) |
        Q(author__username__icontains=q)
    ).distinct()

    serializer = ArticleSerializer(qs, many=True)
    return Response({'count': len(serializer.data), 'results': serializer.data})
```
url:
``` python
# myapp/urls.py
from django.urls import path
from .views import search

urlpatterns = [
    path('api/search/', search, name='api-search'),
]
```
Example request:
``` bash
GET /api/search/?q=django
```
Example response:
``` json
{
  "count": 2,
  "results": [
    {
      "id": 1,
      "title": "Django tips",
      "body": "Some article text...",
      "author_username": "admin",
      "created": "2025-08-10T15:32:00Z"
    },
    {
      "id": 2,
      "title": "Learning Django",
      "body": "More article text...",
      "author_username": "john",
      "created": "2025-08-10T15:40:00Z"
    }
  ]
}
```


## Algolia Search Engines:
You _can_ implement search with Django’s ORM filters, but services like **Algolia**, **Meilisearch**, or **Elasticsearch** exist because they solve several hard problems that arise once your app’s search needs go beyond "basic filter + order."

Here’s the breakdown:
### **1. Speed and Scalability**

- **Django filtering**: Works fine for small datasets, but once you have millions of rows, complex `icontains` queries will be slow unless you invest heavily in database indexing and optimization.
    
- **Algolia**: Designed for sub-50ms search responses, even with millions of records, thanks to specialized inverted indexes and distributed infrastructure.
    

---

### **2. Search Relevance**

- **Django**: Out of the box, you only get exact matches or basic substring search. “apple” won’t rank above “pineapple” unless you explicitly code ranking logic.
    
- **Algolia**: Has built-in relevance scoring, typo tolerance, partial word matching, synonyms, and field weight boosting without you reinventing the wheel.
    

---

### **3. Rich Features**

Algolia and similar services give you:

- **Fuzzy matching** (typo tolerance)
    
- **Synonym handling**
    
- **Faceted search** (filtering by categories, price ranges, etc.)
    
- **Highlighting** matched terms
    
- **Geo-search**
    
- **Instant search-as-you-type**
    

Doing all of that in Django manually would require custom search indexes, a ranking algorithm, and possibly even a separate full-text search engine like Postgres FTS.

---

### **4. Reduced Load on Your Main DB**

- Without an external search engine, search queries hammer your primary database.
    
- Algolia offloads all search traffic to their infrastructure, freeing your DB for critical transactions.
    

---

### **5. Maintenance & Dev Time**

- **DIY search in Django**:
    
    - Implement ranking and scoring.
        
    - Maintain indexes.
        
    - Handle performance tuning.
        
    - Manage scaling and caching.
        
- **Algolia**:
    
    - You configure fields, push your data, and focus on the UX.
        
    - Maintenance is essentially handled for you.
        

---

### **When you _don’t_ need Algolia**

Stick to Django queries if:

- Your dataset is small (tens or hundreds of thousands of rows).
    
- You only need simple filtering (no relevance scoring, no typo tolerance).
    
- You’re fine with search results being a bit slower for large queries.
    
- You don’t want to pay for an external service.

#### Steps to get it working:
##### In your app directory, create a new file `algolia_registry.py`:
``` python
import algoliasearch_django as algoliasearch
from .models import Product

@algoliasearch.register(Product)
class ProductIndex(algoliasearch.AlgoliaIndex):
    fields = ('name', 'description', 'price')
    settings = {
        'searchableAttributes': ['name', 'description'],
        'attributesForFaceting': ['price']
    }
    index_name = 'products'
```
##### now migrate, then push the existing data to algolia:
``` bash
python manage.py algolia_reindex
```

#### Creating a Django search view:
``` python
# views.py
from django.conf import settings
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from algoliasearch.search_client import SearchClient
from .serializers import ProductSearchSerializer

class ProductSearchSerializer(serializers.Serializer):
    name = serializers.CharField()
    description = serializers.CharField()
    price = serializers.FloatField()

class ProductSearchAPIView(APIView):
    """
    Search products from Algolia index.
    """
    def get(self, request):
        query = request.query_params.get('q', '')

        if not query:
            return Response({"error": "Missing search query"}, status=status.HTTP_400_BAD_REQUEST)

        client = SearchClient.create(
            settings.ALGOLIA['APPLICATION_ID'],
            settings.ALGOLIA['SEARCH_API_KEY']  # Search-only key
        )

        index = client.init_index('products')
        results = index.search(query)

        serializer = ProductSearchSerializer(results.get('hits', []), many=True)

        return Response({
            "query": query,
            "nbHits": results.get('nbHits', 0),
            "hits": serializer.data
        }, status=status.HTTP_200_OK)
```

## Adding search to admin panel Table entry:
``` python
# admin.py
from django.contrib import admin
from .models import Quote

@admin.register(Quote)
class QuoteAdmin(admin.ModelAdmin):
	# fields that we will allow to be shown in the admin panel
	list_display = ('quote_text', 'quote_author', 'quote_source')
	# fields where search field will search queries through
	search_fields = ('quote_text', 'quote_author', 'quote_source')
```
