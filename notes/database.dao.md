---
id: wbpxvctt9swtycdqvefycrw
title: Data Access Object (DAO) Pattern
desc: ''
updated: 1668101982067
created: 1668100452258
---

## A Design Pattern
DAO is a design pattern to minimize coupling between your application and your backend where ORM deals with how to map objects into an object-relational database. This reduce the coupling between the database and your application.

Potential disadvantages of using DAO include leaky abstraction, code duplication, and abstraction inversion.

## Example
[dao-vs-ormhibernate-pattern](https://stackoverflow.com/questions/4037251/dao-vs-ormhibernate-pattern/4037454#4037454)

```java
public class Application
{
    private UserDao userDao;

    public Application(UserDao dao)
    {
        // Get the actual implementation
        // e.g. through dependency injection
        this.userDao = dao;
    }

    public void login()
    {
        // No matter from where
        User = userDao.findByUsername("Dummy");
    }
}


public interface UserDao
{
    User findByUsername(String name);
}

public class HibernateUserDao implements UserDao
{
    public User findByUsername(String name)
    {
        // Do some Hibernate specific stuff
        this.session.createQuery...
    }
}

public class SqlUserDao implements UserDao
{
    public User findByUsername(String name)
    {
        String query = "SELECT * FROM users WHERE name = '" + name + "'";
        // Execute SQL query and do mapping to the object
    }
}

public class LdapUserDao implements UserDao
{
    public User findByUsername(String name)
    {
        // Get this from LDAP directory
    }
}

public class NoSqlUserDao implements UserDao
{
    public User findByUsername(String name)
    {
        // Do something with e.g. couchdb
        ViewResults resultAdHoc = db.adhoc("function (doc) { if (doc.name=='" + name + "') { return doc; }}");
        // Map the result document to user
    }
}
```