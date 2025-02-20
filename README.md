# **Django One-to-Many Relationship Tutorial**

## **Step 1: Create a Django Project and App**
If you haven’t created a project yet, start with:

```sh
django-admin startproject myproject
cd myproject
python manage.py startapp bookapp
```

### **Best Practice: Register the App in `settings.py`**
Open `myproject/settings.py` and add `'bookapp'` to `INSTALLED_APPS`:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'bookapp',  # Registering our app
]
```

---

## **Step 2: Define Models with One-to-Many Relationship**
Now, open `bookapp/models.py` and define the two models:

- **Author (parent)**
- **Book (child, linked to Author with a One-to-Many relationship)**

```python
from django.db import models

class Author(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField(unique=True)

    def __str__(self):
        return self.name  # Best practice: always define __str__()

class Book(models.Model):
    title = models.CharField(max_length=200)
    publication_date = models.DateField()
    author = models.ForeignKey(Author, on_delete=models.CASCADE, related_name="books")

    def __str__(self):
        return self.title
```

### **Understanding Best Practices Here:**
✅ Use `__str__()` method for meaningful object representation.  
✅ Set `unique=True` for the `email` field to avoid duplicate authors.  
✅ Use `related_name="books"` to make reverse lookup easier (`author.books.all()`).  
✅ Use `on_delete=models.CASCADE` to delete all books if the author is deleted.  

---

## **Step 3: Generate and Apply Migrations**
Now, let's generate and apply migrations.

```sh
python manage.py makemigrations
python manage.py migrate
```

### **What Happens Here?**
- `makemigrations` creates migration files (`bookapp/migrations/0001_initial.py`).
- `migrate` applies those migrations, creating **`author`** and **`book`** tables.

---

## **Step 4: Using Django ORM to Add Data**
Now, let's use the Django **ORM** to insert and retrieve data.

### **Open Django Shell**
```sh
python manage.py shell
```

### **Create an Author**
```python
from bookapp.models import Author, Book

author1 = Author.objects.create(name="J.K. Rowling", email="jk@example.com")
author2 = Author.objects.create(name="George R.R. Martin", email="george@example.com")
```

### **Create Books (One-to-Many Relationship)**
```python
book1 = Book.objects.create(title="Harry Potter and the Sorcerer's Stone", publication_date="1997-06-26", author=author1)
book2 = Book.objects.create(title="Harry Potter and the Chamber of Secrets", publication_date="1998-07-02", author=author1)

book3 = Book.objects.create(title="A Game of Thrones", publication_date="1996-08-06", author=author2)
book4 = Book.objects.create(title="A Clash of Kings", publication_date="1998-11-16", author=author2)
```
✅ **Now we have two authors, each with multiple books!**

---

## **Step 5: Querying the Database**
Now, let’s practice **fetching data** using Django ORM.

### **Retrieve All Authors**
```python
authors = Author.objects.all()
for author in authors:
    print(author.name)
```

### **Retrieve a Specific Author and Their Books**
```python
author = Author.objects.get(name="J.K. Rowling")
books = author.books.all()  # Using related_name
for book in books:
    print(book.title)
```

### **Find Books by a Specific Author (Filter)**
```python
books_by_george = Book.objects.filter(author=author2)
for book in books_by_george:
    print(book.title)
```

---

## **Step 6: Updating and Deleting Records**

### **Update an Author’s Email**
```python
author = Author.objects.get(name="J.K. Rowling")
author.email = "rowling_updated@example.com"
author.save()
```

### **Delete a Book**
```python
book = Book.objects.get(title="A Clash of Kings")
book.delete()
```

### **Delete an Author (Cascade Effect)**
```python
author = Author.objects.get(name="George R.R. Martin")
author.delete()  # This will delete all books related to this author
```

---

## **Step 7: Best Practices Recap**

### ✅ **Model Best Practices**
- **Use `__str__()`** to display meaningful object names.
- **Use `related_name` in ForeignKey** for easier reverse lookups.
- **Use `unique=True`** where needed (e.g., `email` for `Author`).
- **Use `on_delete=models.CASCADE`** if child objects should be deleted when the parent is removed.

### ✅ **Migration Best Practices**
- **Always run `makemigrations` after modifying models.**
- **Run `migrate` to apply changes before using models.**
- **Use `showmigrations` to check applied migrations:**
  ```sh
  python manage.py showmigrations
  ```

### ✅ **Django ORM Best Practices**
- **Use `get()` when expecting only one result, but handle exceptions:**
  ```python
  try:
      author = Author.objects.get(name="J.K. Rowling")
  except Author.DoesNotExist:
      print("Author not found!")
  ```
- **Use `filter()` when expecting multiple results to avoid `DoesNotExist` errors.**
- **Use `update()` for batch updates instead of `for` loops for better performance.**

---

## **Final Task: Verify Everything Works**
✅ **Run the Django shell**:
```sh
python manage.py shell
```
✅ **Try inserting, updating, and deleting data using Django ORM.**  
✅ **Check relationships (`author.books.all()`) work as expected.**  

🚀 **Now you have built a Django app with a One-to-Many relationship using best practices!**
