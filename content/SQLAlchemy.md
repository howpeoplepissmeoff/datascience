---
tags:
  - datascience
date: " 2025-02-20"
up: "[[SQL]]"
status: "#framework"
---
# Engine


>[!note] What is that?
> The [`Engine`](https://docs.sqlalchemy.org/en/21/core/connections.html#sqlalchemy.engine.Engine "sqlalchemy.engine.Engine") is the starting point for any SQLAlchemy application. It’s “home base” for the actual database and its [DBAPI](https://docs.sqlalchemy.org/en/21/glossary.html#term-DBAPI), delivered to the SQLAlchemy application through a connection pool and a [`Dialect`](https://docs.sqlalchemy.org/en/21/core/internals.html#sqlalchemy.engine.Dialect "sqlalchemy.engine.Dialect"), which describes how to talk to a specific kind of database/DBAPI combination.

![[Pasted image 20250220114542.png]]


above, an [`Engine`](https://docs.sqlalchemy.org/en/21/core/connections.html#sqlalchemy.engine.Engine "sqlalchemy.engine.Engine") references both a [`Dialect`](https://docs.sqlalchemy.org/en/21/core/internals.html#sqlalchemy.engine.Dialect "sqlalchemy.engine.Dialect") and a [`Pool`](https://docs.sqlalchemy.org/en/21/core/pooling.html#sqlalchemy.pool.Pool "sqlalchemy.pool.Pool"), which together interpret the DBAPI’s module functions as well as the behavior of the database.

## Create an engine

- `create_engine`

>[!abstract] Syntax
>```python
> from sqlalchemy import create_engine
> engine = create_engine("postgresql+psycopg2://scott:tiger@localhost:5432/mydatabase")
>```

The above engine creates:
- a [`Dialect`](https://docs.sqlalchemy.org/en/21/core/internals.html#sqlalchemy.engine.Dialect "sqlalchemy.engine.Dialect") object tailored towards PostgreSQL
- a [`Pool`](https://docs.sqlalchemy.org/en/21/core/pooling.html#sqlalchemy.pool.Pool "sqlalchemy.pool.Pool") object which will establish a DBAPI connection at `localhost:5432` 


The [`Engine`](https://docs.sqlalchemy.org/en/21/core/connections.html#sqlalchemy.engine.Engine "sqlalchemy.engine.Engine"), once created, can either be used directly to interact with the database, or can be passed to a [`Session`](https://docs.sqlalchemy.org/en/21/orm/session_api.html#sqlalchemy.orm.Session "sqlalchemy.orm.Session") object to work with the ORM. 


Here's an example

```python
url = URL.create(
    "mysql+pymysql",
    username="root",
    host="172.28.144.1",
    password='ciao1234',
    port=3306,
    database="world_layoffs"
)

engine = create_engine(url)

  

print(engine)
```


### Dialects and drivers

The [`create_engine()`](https://docs.sqlalchemy.org/en/21/core/engines.html#sqlalchemy.create_engine "sqlalchemy.create_engine") function produces an [`Engine`](https://docs.sqlalchemy.org/en/21/core/connections.html#sqlalchemy.engine.Engine "sqlalchemy.engine.Engine") object based on a URL. 

URLs typically include
- username, 
- password, 
- hostname,
- database name fields

The typical form of a database URL is:

```python
dialect+driver://username:password@host:port/database
```

- `Dialect`: names include the identifying name of the SQLAlchemy dialect, a name such as `sqlite`, `mysql`, `postgresql`, `oracle`, or `mssql`. 
- `drivername`: is the name of the DBAPI to be used to connect to the database using all lowercase letters. If not specified, a “default” DBAPI will be imported if available - this default is typically the most widely known driver available for that backend.


For example 

#### PostgreSQL

>[!example] 
>```python
> # default
engine = create_engine("postgresql://scott:tiger@localhost/mydatabase")
># psycopg2
>engine = create_engine("postgresql+psycopg2://scott:tiger@localhost/mydatabase")
># pg8000
>engine = create_engine("postgresql+pg8000://scott:tiger@localhost/mydatabase")
>```

#### Mysql

>[!example] 
>```python
># default
>engine = create_engine("mysql://scott:tiger@localhost/foo")
># mysqlclient (a maintained fork of MySQL-Python)
>engine = create_engine("mysql+mysqldb://scott:tiger@localhost/foo")
># PyMySQL
>engine = create_engine("mysql+pymysql://scott:tiger@localhost/foo")
>```

# Metadata


>[!note] What is that?
> [`MetaData`](https://docs.sqlalchemy.org/en/20/core/metadata.html#sqlalchemy.schema.MetaData "sqlalchemy.schema.MetaData") is a container object that keeps together many different features of a database (or multiple databases) being described.

## Table


To represent a table, use the [`Table`](https://docs.sqlalchemy.org/en/20/core/metadata.html#sqlalchemy.schema.Table "sqlalchemy.schema.Table") class. Its two primary arguments are the table name, then the [`MetaData`](https://docs.sqlalchemy.org/en/20/core/metadata.html#sqlalchemy.schema.MetaData "sqlalchemy.schema.MetaData") object which it will be associated with. The remaining positional arguments are mostly [`Column`](https://docs.sqlalchemy.org/en/20/core/metadata.html#sqlalchemy.schema.Column "sqlalchemy.schema.Column") objects describing each column



```python
from sqlalchemy import Table, Column, Integer, String

user = Table(
    "user",
    metadata_obj,
    Column("user_id", Integer, primary_key=True),
    Column("user_name", String(16), nullable=False),
    Column("email_address", String(60)),
    Column("nickname", String(50), nullable=False),
)
```


Above, a table called `user` is described, which contains four columns. The primary key of the table consists of the `user_id` column. Multiple columns may be assigned the `primary_key=True` flag which denotes a multi-column primary key, known as a _composite_ primary key.



Here's an example

```python
# define a schema
metadata = sa.MetaData()

# create a table
table = sa.Table(
    "uffa",
    metadata,
    sa.Column("name", sa.VARCHAR(50),primary_key=True),
    sa.Column("surname", sa.VARCHAR(50),nullable=False),
    sa.Column("date", sa.DateTime,primary_key=True, nullable=False),
)
```

### Insert data into a table

1. Call the method `Table.insert()`
2. open a connection `with`
3. `execte(stmt, data)`
4. `commmit`


```python
import sqlalchemy as sa
import datetime

  

# define a schema
metadata = sa.MetaData()

  

# create a table
table = sa.Table(
    "uffa",
    metadata,
    sa.Column("name", sa.VARCHAR(50),primary_key=True),
    sa.Column("surname", sa.VARCHAR(50),nullable=False),
    sa.Column("date", sa.DateTime,primary_key=True, nullable=False),
)

  
# add data
stmt = table.insert().values(name = "jacopo",
                      surname = "manenti",
                      date = datetime.datetime.now())

  
# open a connection
with engine.connect() as conn:
	# insert the table
    metadata.create_all(conn)
	# add data in the table
    conn.execute(stmt)
	# save
    conn.commit()
	# close connection
    conn.close()
```


I'll explain SQLAlchemy's `table.insert()` operation, which is used to create INSERT statements for adding data to database tables.

Basic Usage:
```python
from sqlalchemy import Table, insert

# Simple insert
stmt = table.insert().values(column1='value1', column2='value2')
```

Let's go through the main ways to use `insert()`:

1. Single Row Insert:
```python
# Define the insert statement
stmt = users_table.insert().values(
    name='John',
    email='john@example.com',
    age=30
)

# Execute
with engine.connect() as conn:
    result = conn.execute(stmt)
    conn.commit()
```

1. Multiple Rows Insert:
```python
# List of dictionaries for multiple rows
data = [
    {'name': 'John', 'email': 'john@example.com'},
    {'name': 'Alice', 'email': 'alice@example.com'},
    {'name': 'Bob', 'email': 'bob@example.com'}
]

# Execute multiple inserts
stmt = users_table.insert()
with engine.connect() as conn:
    conn.execute(stmt, data)
    conn.commit()
```

1. Insert with Returning Clause (supported by PostgreSQL):
```python
# Insert and return specific columns
stmt = users_table.insert().returning(
    users_table.c.id, 
    users_table.c.name
)

with engine.connect() as conn:
    result = conn.execute(stmt, {"name": "John"})
    returned_data = result.fetchone()
```

1. Insert From Select:
```python
# Insert data from another table
stmt = users_table.insert().from_select(
    ['name', 'email'],
    select(temp_table.c.name, temp_table.c.email)
)
```

1. Insert with Default Values:
```python
# Insert using column defaults
stmt = users_table.insert().values(
    name='John',
    created_at=func.now()  # SQL function
)
```

1. Parameterized Insert:
```python
# Create parameterized statement
stmt = users_table.insert().values(
    name=bindparam('username'),
    email=bindparam('user_email')
)

# Execute with parameters
params = {'username': 'John', 'user_email': 'john@example.com'}
with engine.connect() as conn:
    conn.execute(stmt, params)
    conn.commit()
```

1. Insert or Ignore (dialect-specific):
```python
# SQLite example
stmt = users_table.insert().prefix_with('OR IGNORE')

# PostgreSQL example
stmt = users_table.insert().on_conflict_do_nothing()
```

1. Insert with Relationships (using ORM):
```python
from sqlalchemy.orm import Session

# Create related objects
new_user = User(name='John')
new_user.addresses = [
    Address(email='john@work.com'),
    Address(email='john@home.com')
]

# Insert with relationships
with Session(engine) as session:
    session.add(new_user)
    session.commit()
```

Important Tips:
- Always use a context manager (`with` statement) to properly manage connections
- Remember to call `commit()` after executing inserts
- Use parameterized statements to prevent SQL injection
- Consider using `executemany()` for bulk inserts
- Be aware of database-specific features and syntax

Would you like me to explain any of these aspects in more detail?

### Query a table

```python
username = "jacopo"

with engine.connect() as conn:

    query = table.select().where(table.c.name == username)

    result = conn.execute(query)

    print( result.fetchall())
```


# ORM 

## Walkthrough

- `set up`
```python
# create the engine

url = URL.create(
    "mysql+pymysql",
    username="root",
    host="172.28.144.1",
    password='ciao1234',
    port=3306,
    database="world_layoffs"

)
engine = create_engine(url)


# create the session
Session = sessionmaker(bind = engine)

# create Base
Base = declarative_base()
```


- create a model 
```python
from sqlalchemy.orm import (
    declarative_base,
    mapped_column,
    relationship,
    sessionmaker,
    Mapped,
)

  

from sqlalchemy import ForeignKey

  

class User(Base):
    __tablename__ = "user"
    __table_args__ = {'extend_existing': True}  # Add this line

  
  

    # create columns
    id: Mapped[int] = mapped_column(primary_key=True, autoincrement=True, unique=True, type_= sa.INTEGER)
    username: Mapped[str] = mapped_column(sa.VARCHAR(50))
    email: Mapped[str] = mapped_column(sa.VARCHAR(50))

  
  
    def __repr__(self) ->str:
        return f"<User(id={self.id}, username={self.username}, email={self.email})>"
```

- create all the table

```python
Base.metadata.create_all(engine)
```

- add item 
```python
# add an item

  
  

with Session() as session:
    user = User(username = "miao", email = "miao@gmail.com")
    session.add(user)
    session.commit()
    result = session.query(User).all()
    print(result)
```


- delete item
```python
# delete an item

  

with Session() as session:

    session.delete(user)
    session.commit()
    result = session.query(User).all()
    print(result)
```


- add relationship

```python
class Posts(Base):

    __tablename__="posts"

    __table_args__ = {'extend_existing': True}  # Add this line

  
  

    id : Mapped[int] = mapped_column(primary_key=True, autoincrement=True, unique=True, type_= sa.INTEGER)

    title : Mapped[str] = mapped_column(sa.VARCHAR(50))

    content : Mapped[str] = mapped_column(sa.VARCHAR(50))

  

    # one to many

    user_id : Mapped[int] = mapped_column(ForeignKey('user.id'), type_= sa.INTEGER)

  

    user: Mapped["User"] = relationship('User', back_populates='posts')

  

    def __repr__(self) ->str:

        return f"<User(id={self.id}, username={self.title}, email={self.content}), user_id={self.user_id}, user={self.user})>"

  
  

class User(Base):

    __tablename__ = "user"

    __table_args__ = {'extend_existing': True}  # Add this line

  
  

    # create columns

    id: Mapped[int] = mapped_column(primary_key=True, autoincrement=True, unique=True, type_= sa.INTEGER)

    username: Mapped[str] = mapped_column(sa.VARCHAR(50))

    email: Mapped[str] = mapped_column(sa.VARCHAR(50))

  

    # create a relationship on both sides

    posts: Mapped[List["Posts"]] = relationship('Posts', back_populates= 'user')

  

    def __repr__(self) ->str:

        return f"<User(id={self.id}, username={self.username}, email={self.email}, posts= {self.posts})>"
```


- join
```python
# join


with Session() as session:

    result = session.query(Posts, User).join(User).all()
    for p,u in result:
        print(p)
        print(u)
```

- filter 

```python
with Session() as session:

    flavio = session.query(User).filter_by(username = "flavio").first()

    for p in flavio.posts:

        print(p.content)
```


## Session


>[!note] What is that?
> A session factory is a function that creates new Session objects. Think of it like a blueprint for creating database sessions.

>[!abstract] Syntax
>


```python
from sqlalchemy.orm import sessionmaker

# Create session factory
Session = sessionmaker(bind=engine)

# Using the factory to create sessions
session1 = Session()  # Creates new session
session2 = Session()  # Creates another independent session
```

For example

```python
# Configure session factory with additional options
Session = sessionmaker(
    bind=engine,
    autoflush=True,
    expire_on_commit=False,
    autocommit=False
)

# Now all sessions created will have these settings
with Session() as session:
    user = User(name="John")
    session.add(user)
    session.commit()
```

## Base


>[!note] What is that?
>The Base class is a foundation for all your database model classes. It provides the necessary functionality to convert Python classes into database tables.

>[!abstract] Syntax
>


```python
from sqlalchemy.orm import declarative_base

# Create the base class
Base = declarative_base()

# Use Base to create model classes
class User(Base):
    __tablename__ = "users"
    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str]

class Product(Base):
    __tablename__ = "products"
    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str]
```

## Create a table


- table columns are declared inline within the mapped class using the [`mapped_column()`](https://docs.sqlalchemy.org/en/20/orm/mapping_api.html#sqlalchemy.orm.mapped_column "sqlalchemy.orm.mapped_column") directive 
- The [`mapped_column()`](https://docs.sqlalchemy.org/en/20/orm/mapping_api.html#sqlalchemy.orm.mapped_column "sqlalchemy.orm.mapped_column") directive may also be optionally combined with type annotations using the [`Mapped`](https://docs.sqlalchemy.org/en/20/orm/internals.html#sqlalchemy.orm.Mapped "sqlalchemy.orm.Mapped") class which can provide some details about the mapped columns directly. 
- The column directives, in combination with the `__tablename__` and optional `__table_args__` class level directives will allow the Declarative mapping process to construct a [`Table`](https://docs.sqlalchemy.org/en/20/core/metadata.html#sqlalchemy.schema.Table "sqlalchemy.schema.Table") object to be mapped.

`mapped_column` is a class introduced in SQLAlchemy 2.0 that defines how a Python class attribute maps to a database column.

Basic Syntax:
```python
from sqlalchemy.orm import Mapped, mapped_column

class User(Base):
    __tablename__ = "users"
    
    # Basic mapped column
    id: Mapped[int] = mapped_column(primary_key=True)
```

Here are the main features of `mapped_column`:

1. Basic Column Types:
```python
class User(Base):
    __tablename__ = "users"
    
    # Different column types
    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = mapped_column(String(50))
    age: Mapped[int] = mapped_column(Integer)
    is_active: Mapped[bool] = mapped_column(Boolean)
    created_at: Mapped[datetime] = mapped_column(DateTime)
```

1. Column Constraints:
```python
class User(Base):
    # Not null constraint
    username: Mapped[str] = mapped_column(String(50), nullable=False)
    
    # Unique constraint
    email: Mapped[str] = mapped_column(String(100), unique=True)
    
    # Default value
    is_active: Mapped[bool] = mapped_column(Boolean, default=True)
    
    # Server default (SQL-level default)
    created_at: Mapped[datetime] = mapped_column(
        DateTime, 
        server_default=func.now()
    )
```

1. Foreign Keys:
```python
class Post(Base):
    __tablename__ = "posts"
    
    id: Mapped[int] = mapped_column(primary_key=True)
    # Foreign key to User table
    user_id: Mapped[int] = mapped_column(ForeignKey("users.id"))
```

1. Indexes and Constraints:
```python
class User(Base):
    email: Mapped[str] = mapped_column(
        String(100),
        unique=True,
        index=True  # Creates an index
    )
    
    # Composite unique constraint
    username: Mapped[str] = mapped_column(
        String(50),
        unique=True,
        comment="User's display name"  # Database comment
    )
```

1. Optional Fields:
```python
class User(Base):
    # Optional field (can be None)
    middle_name: Mapped[Optional[str]] = mapped_column(String(50))
    
    # Required field
    first_name: Mapped[str] = mapped_column(String(50), nullable=False)
```

1. Advanced Features:
```python
class Product(Base):
    # Auto-incrementing primary key
    id: Mapped[int] = mapped_column(
        Integer,
        primary_key=True,
        autoincrement=True
    )
    
    # Decimal precision
    price: Mapped[Decimal] = mapped_column(
        Numeric(10, 2),  # 10 digits, 2 decimal places
        nullable=False
    )
    
    # Enum type
    status: Mapped[str] = mapped_column(
        Enum('active', 'inactive', 'pending'),
        default='pending'
    )
```

Usage example:
```python
class User(Base):
    __tablename__ = "users"
    
    # Primary key
    id: Mapped[int] = mapped_column(primary_key=True)
    
    # Required string field with max length
    username: Mapped[str] = mapped_column(
        String(50),
        nullable=False,
        unique=True
    )
    
    # Optional email field
    email: Mapped[Optional[str]] = mapped_column(
        String(100),
        unique=True,
        index=True
    )
    
    # Timestamp with default
    created_at: Mapped[datetime] = mapped_column(
        DateTime,
        default=datetime.utcnow
    )

# Using the model
user = User(
    username="john_doe",
    email="john@example.com"
)
```

### Relationship

- `uselist=False`: This tells SQLAlchemy that the relationship is one-to-one instead of one-to-many. Without it, SQLAlchemy would return a list even for a single related object

```python
class User(Base): 
__tablename__ = "users" 

id = Column(Integer, primary_key=True) 

# One-to-one: one user has one profile 
profile = relationship("Profile", uselist=False, back_populates="user") 

# One-to-many: one user has many posts (default behavior) 
posts = relationship("Post", back_populates="user") 

# With uselist=False: 
user.profile # Returns a single Profile object 
user.posts # Returns a list of Post objects
```


- `back_populates`: Creates a bidirectional relationship between tables. Think of `back_populates` as creating a two-way connection between related tables. When you update one side, the other side automatically stays in sync.

```python
class Parent(Base):
    __tablename__ = "parents"
    id = Column(Integer, primary_key=True)
    name = Column(String)
    
    # This creates the link to Child
    children = relationship("Child", back_populates="parent")

class Child(Base):
    __tablename__ = "children"
    id = Column(Integer, primary_key=True)
    name = Column(String)
    parent_id = Column(Integer, ForeignKey('parents.id'))
    
    # This creates the link back to Parent
    parent = relationship("Parent", back_populates="children")

# Now you can do this:
with Session() as session:
    # Create parent and child
    parent = Parent(name="John")
    child = Child(name="Tommy")
    
    # Connect them
    parent.children.append(child)
    
    # You can access both ways:
    print(parent.children)      # [<Child 'Tommy'>]
    print(child.parent)         # <Parent 'John'>
    
    # If you update one side, the other updates automatically
    new_child = Child(name="Sarah")
    parent.children.append(new_child)
    print(new_child.parent.name)  # "John"
```




Here are the key concepts from this SQLAlchemy code:

1. Database Setup & Configuration:
```python
# Creates in-memory SQLite database
db = sa.create_engine("sqlite:///:memory:")
# Creates session factory for database operations
Session = sessionmaker(bind=db)
# Creates base class for models
Base = declarative_base()
```

1. Relationships:
- One-to-One: User ↔ UserAuth
  ```python
  # In User class
  auth: Mapped["UserAuth"] = relationship("UserAuth", uselist=False, back_populates="user")
  ```
- One-to-Many: User ↔ UserPost
  ```python
  # In User class
  posts: Mapped[typing.List["UserPost"]] = relationship("UserPost", back_populates="user")
  ```

1. Foreign Key Relationships:
```python
# UserAuth's id is both primary key and foreign key to User
id: int = sa.Column(sa.Integer, sa.ForeignKey("users.id"), primary_key=True)

# UserPost's user_id is foreign key to User
user_id: int = sa.Column(sa.Integer, sa.ForeignKey("users.id"), nullable=False)
```

1. Password Handling:
```python
def set_password(self, password: str) -> None:
    # Converts password to hash using SHA-256
    self.password_hash = hashlib.sha256(password.encode()).hexdigest()
```

1. Session Management:
```python
with Session.begin() as session:
    # Automatic transaction handling
    session.add(user)
    session.add(post)
    # Auto-commits at end of block
```

1. Column Types and Constraints:
```python
# Primary key with auto-increment
id: Mapped[int] = mapped_column(primary_key=True)

# Unique constraint
email: str = sa.Column(sa.String, unique=True)

# Non-null constraint
user_id: int = sa.Column(sa.Integer, nullable=False)
```

1. Model Initialization:
```python
def __init__(self, username: str, email: str, password: str):
    super().__init__()  # Initialize SQLAlchemy Base
    # Create UserAuth instance with user data
    self.auth = UserAuth(username=username, email=email)
```

1. Querying:
```python
# Get first user
user = session.query(User).first()

# Filter posts by user
posts = session.query(UserPost).filter(UserPost.user == user).all()
```

1. Model Representation:
```python
def __repr__(self) -> str:
    # Custom string representation for debugging
    return f"<User(username={self.auth.username}, email={self.auth.email})>"
```

1. Type Hints:
```python
# Modern SQLAlchemy type hints
id: Mapped[int] = mapped_column(primary_key=True)
posts: Mapped[typing.List["UserPost"]] = relationship(...)
```

Important Design Patterns:
- Separation of concerns (auth logic separate from user data)
- Bidirectional relationships (`back_populates`)
- Password hashing for security
- Transaction management with context managers
- Type hints for better code clarity

### Add multiple values

```python
users = [ User(username="john", email="john@example.com", password="pass1"), User(username="alice", email="alice@example.com", password="pass2"), User(username="bob", email="bob@example.com", password="pass3") ] 

with Session() as session: 
	session.add_all(users) 
	session.commit()
```

```python
user_data = [
    {"username": "john", "email": "john@example.com", "password": "pass1"},
    {"username": "alice", "email": "alice@example.com", "password": "pass2"},
    {"username": "bob", "email": "bob@example.com", "password": "pass3"}
]

with Session() as session:
    for data in user_data:
        user = User(**data)
        session.add(user)
    session.commit()
```

## Interact with existing table

- `autoload_with=engine`
```python
from sqlalchemy import Table, MetaData, select, insert, update, delete
from sqlalchemy.orm import Session

# First define the table mapping
metadata = MetaData()
posts = Table('posts', metadata, autoload_with=engine)

# Create a session
session = Session(engine)
```

```python

# 1. Select/Query data
# Get all posts
query = select(posts)
result = session.execute(query)
all_posts = result.fetchall()

# Get posts with conditions
query = select(posts).where(posts.c.user_id == 1)
result = session.execute(query)
user_posts = result.fetchall()

```

```python
# 2. Insert data
new_post = {
    'title': 'New Post',
    'content': 'Content here',
    'user_id': 1
}
query = insert(posts).values(new_post)
session.execute(query)
session.commit()


```

```python
# 3. Update data
query = (
    update(posts)
    .where(posts.c.id == 1)
    .values(title='Updated Title')
)
session.execute(query)
session.commit()

```

```python
# 4. Delete data
query = delete(posts).where(posts.c.id == 1)
session.execute(query)
session.commit()

```

```python
# 5. Join with user table (assuming you have user table defined similarly)
users = Table('user', metadata, autoload_with=engine)
join_query = select(posts, users).join(users, posts.c.user_id == users.c.id)
result = session.execute(join_query)
joined_data = result.fetchall()

# Note: When using Table construct, use .c to access columns
# For example: posts.c.id, posts.c.title, etc.
```

# Related:

---
# Reference

1. https://www.youtube.com/watch?v=xr7vDSFXjW0
2. https://github.com/ArjanCodes/examples/blob/main/2024/sqlalchemy/relationship.py
