---
id: qgh5ypgf2ed0blahec22bl7
title: ORMs
desc: ''
updated: 1668100313380
created: 1668097443787
---

## Object Relational Mappers (ORMs)
An object-relational mapper (ORM) is a code library that automates the transfer of data stored in relational database tables into objects that are more commonly used in application code.

## ORM Tools For Python
1. [SQLAlchemy](https://www.sqlalchemy.org/)
2. [Peewee](http://docs.peewee-orm.com/en/latest/)
3. [The Django ORM](https://docs.djangoproject.com/en/dev/topics/db/)
4. [PonyORM](https://ponyorm.org/)
5. [SQLObject](http://sqlobject.org/)
6. [Tortoise ORM](https://tortoise-orm.readthedocs.io/en/latest/)

ORMs @ Full Stack Python [link](https://www.fullstackpython.com/object-relational-mappers-orms.html)

## Example Implementation
To-do List App [[Rodolfo Campos, hackernoon.com, Oct. 15, 2021](https://hackernoon.com/building-a-to-do-list-app-with-python-data-access-layer-with-sqlalchemy)]

The actual implementation has the following components:
1. Schema. Defines the database schema and provides connections wrapped in a Transaction Manager that keeps the database conversational state.
2. BasRepo. It’s an abstract repository that implements the main 4 operations of a simple CRUD. Assuming that every table uses an auto-increment id field.
3. UserRepo and tests. Extends BaseRepo for Users.
4. TodRepo and tests. Extends BaseRepo for To-dos.

## Schema
About the implementation:

- Uses an env variable for creating the engine
- Defines the DB schema
- Offers methods to create and drop the DB schema
- Offers a Context Manager (TransactionManager) that wraps the connection and handles the DB conversational state. Easy to use with Python with statements.

```python
import os
from sqlalchemy import (
    create_engine, 
    Boolean,
    Column, 
    ForeignKey,
    Integer, 
    MetaData, 
    String,
    Table
)

class Schema:
    def __init__(self):
        self.engine = create_engine(os.getenv("DB_URI"), echo=True, future=True)
        self.metadata = MetaData()
        self.tables = self.__generate_tables()

    def create_transaction(self):
        return TransactionManager(self)        

    def create_all_tables(self):
        self.metadata.create_all(self.engine)

    def drop_all_tables(self):
        self.metadata.drop_all(self.engine)

    def __generate_tables(self):
        return {
            'todo': Table('todo', self.metadata,
                Column('id', Integer, primary_key=True , autoincrement=True),
                Column('user_id', Integer, ForeignKey('user.id')),
                Column('description', String(500)),
                Column('active', Boolean)
            ),
            'user': Table('user', self.metadata,
                Column('id', Integer, primary_key=True , autoincrement=True),
                Column('email', String(50)),
                Column('fullname', String(50))
            ),
        }

class TransactionManager:
    def __init__(self, schema):
        self.schema = schema
        self.conn = self.schema.engine.connect()

    def __enter__(self):
        return self

    def __exit__(self, type, value, traceback):
        self.conn.commit()
        self.conn.close()

```

## BaseRepo
About the implementation:

- Assumes that the table is defined in the schema and has an auto-increment id field.
- Separates insert from update. You could implement just a save operation, similar to an upsert.
- Returns cursors when finding all, not an array. Useful when handling several rows.

```python
from abc import ABC, abstractmethod
from sqlalchemy import insert, select, update
from sqlalchemy.exc import InvalidRequestError

class BaseRepo(ABC):
    def __init__(self, tx):
        self.conn = tx.conn
        self.schema = tx.schema

    def find_all(self):
        stmt = select(self._get_table())
        return self.conn.execute(stmt)

    def find_by_id(self, id):
        table = self._get_table()
        stmt = select(table).where(table.c.id == id)
        res = self.conn.execute(stmt)
        if res:
            return res.fetchone()

    def insert(self, user):
        table = self._get_table()
        stmt = insert(table).values(user)
        res = self.conn.execute(stmt)
        return res.inserted_primary_key[0]

    def update(self, user):
        prev_user = self.find_by_id(user['id'])
        if not prev_user:
            raise InvalidRequestError('Invalid id')
        stmt = update(self._get_table()).values(user)
        self.conn.execute(stmt)

    @abstractmethod
    def _get_table(self):
        pass
```

## TodoRepo

About the implementation:

- Extends BaseRepo.
- Implements the only required method that returns the corresponding table from the Schema.
- Any specific data access methods should be implemented here.

```python
from base_repo import BaseRepo

class TodoRepo(BaseRepo):
    def _get_table(self):
        return self.schema.tables['todo']
```

## TodoRepoTest
About the implementation:

- Uses in-memory sqlite DB, injected via env variables.
- Groups in classes tests per each method in TodoRepo.
- Creates a new schema per test.
- Enables foreign constraints.

```python
import pytest
from sqlalchemy import event, insert, select
from sqlalchemy.exc import IntegrityError, InvalidRequestError

from schema import Schema
from todo_repo import TodoRepo

@pytest.fixture
def schema(monkeypatch):
    monkeypatch.setenv("DB_URI", "sqlite://")
    schema = Schema()
    _enable_foreign_constraints(schema)
    schema.create_all_tables()
    return schema

def _enable_foreign_constraints(schema):
    def _fk_pragma_on_connect(dbapi_con, con_record):
        dbapi_con.execute('pragma foreign_keys=ON')
    event.listen(schema.engine, 'connect', _fk_pragma_on_connect)

class TestTodoRepo_find_all:
    def test_when_multiple_todos(self, schema):
        with schema.create_transaction() as tx:
            insert_obj(tx, 'user', { 'id': 1, 'email': 'a@test.com', 'fullname': 'fullname a' })
            insert_obj(tx, 'todo', { 'id': 1, 'user_id': 1, 'description': 'description a', 'active': True })
            insert_obj(tx, 'todo', { 'id': 2, 'user_id': 1, 'description': 'description b', 'active': False })
            todos = TodoRepo(tx).find_all().fetchall()
            assert len(todos) == 2
            assert todos[0]['id'] == 1
            assert todos[0]['user_id'] == 1
            assert todos[0]['description'] == 'description a'
            assert todos[0]['active'] == True
            assert todos[1]['id'] == 2
            assert todos[1]['user_id'] == 1
            assert todos[1]['description'] == 'description b'
            assert todos[1]['active'] == False

    def test_when_empty(self, schema):
        with schema.create_transaction() as tx:
            todos = TodoRepo(tx).find_all().fetchall()
            assert len(todos) == 0

class TestTodoRepo_find_by_id:
    def test_when_todo_exists(self, schema):
        with schema.create_transaction() as tx:
            insert_obj(tx, 'user', { 'id': 1, 'email': 'a@test.com', 'fullname': 'fullname a' })
            insert_obj(tx, 'todo', { 'id': 1, 'user_id': 1, 'description': 'description a', 'active': True })
            insert_obj(tx, 'todo', { 'id': 2, 'user_id': 1, 'description': 'description b', 'active': False })
            todo = TodoRepo(tx).find_by_id(1)
            assert todo['id'] == 1
            assert todo['user_id'] == 1
            assert todo['description'] == 'description a'
            assert todo['active'] == True

    def test_when_todo_does_not_exists(self, schema):
        with schema.create_transaction() as tx:
            todo = TodoRepo(tx).find_by_id(1)
            assert todo is None

class TestTodoRepo_insert:
    def test_when_auto_increment_id(self, schema):
        with schema.create_transaction() as tx:
            insert_obj(tx, 'user', { 'id': 1, 'email': 'a@test.com', 'fullname': 'fullname a' })
            id = TodoRepo(tx).insert({ 'user_id': 1, 'description': 'description a', 'active': True })
            table = tx.schema.tables['todo']
            todos = tx.conn.execute(select(table)).fetchall()
            assert len(todos) == 1
            assert todos[0]['id'] == id
            assert todos[0]['user_id'] == 1
            assert todos[0]['description'] == 'description a'
            assert todos[0]['active'] == True

    def test_when_set_id(self, schema):
        with schema.create_transaction() as tx:
            insert_obj(tx, 'user', { 'id': 1, 'email': 'a@test.com', 'fullname': 'fullname a' })
            TodoRepo(tx).insert({ 'id': 99, 'user_id': 1, 'description': 'description a', 'active': True })
            table = tx.schema.tables['todo']
            todos = tx.conn.execute(select(table)).fetchall()
            assert len(todos) == 1
            assert todos[0]['id'] == 99
            assert todos[0]['user_id'] == 1
            assert todos[0]['description'] == 'description a'
            assert todos[0]['active'] == True

    def test_when_invalid_id(self, schema):
        with schema.create_transaction() as tx, pytest.raises(IntegrityError):
            insert_obj(tx, 'user', { 'id': 1, 'email': 'a@test.com', 'fullname': 'fullname a' })
            TodoRepo(tx).insert({ 'id': 'invalid', 'user_id': 1, 'description': 'description a', 'active': True })

    def test_when_invalid_user_id(self, schema):
        with schema.create_transaction() as tx, pytest.raises(IntegrityError):
            TodoRepo(tx).insert({ 'id': 1, 'user_id': 1, 'description': 'description a', 'active': True })

    def test_when_duplicated_id(self, schema):
        with schema.create_transaction() as tx, pytest.raises(IntegrityError):
            repo = TodoRepo(tx)
            repo.insert({ 'id': 1, 'user_id': 1, 'description': 'description a', 'active': True })
            repo.insert({ 'id': 1, 'user_id': 1, 'description': 'description a', 'active': True })

class TestTodoRepo_update:
    def test_when_todo_exists(self, schema):
        with schema.create_transaction() as tx:
            insert_obj(tx, 'user', { 'id': 1, 'email': 'a@test.com', 'fullname': 'fullname a' })
            insert_obj(tx, 'user', { 'id': 2, 'email': 'b@test.com', 'fullname': 'fullname b' })
            insert_obj(tx, 'todo', { 'id': 1, 'user_id': 1, 'description': 'description a', 'active': True })
            TodoRepo(tx).update({ 'id': 1, 'user_id': 2, 'description': 'description b', 'active': False })
            table = tx.schema.tables['todo']
            todos = tx.conn.execute(select(table)).fetchall()
            assert len(todos) == 1
            assert todos[0]['id'] == 1
            assert todos[0]['user_id'] == 2
            assert todos[0]['description'] == 'description b'
            assert todos[0]['active'] == False

    def test_when_todo_does_not_exists(self, schema):
        with schema.create_transaction() as tx, pytest.raises(InvalidRequestError):
            TodoRepo(tx).update({ 'id': 1, 'user_id': 1, 'description': 'description b', 'active': False })

def insert_obj(tx, table_name, obj):
    table = tx.schema.tables[table_name]
    stmt = insert(table).values(obj)
    tx.conn.execute(stmt)
```