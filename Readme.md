# Codeigniter Core Model Extension

A simple core extension library for Codeigniter that allows simple and flexible database queries using Method Chaining.

By __Mario Cuba__ <[mario@mariocuba.net][email]>

## License

Released to the Public Domain under the [Creative Commons Attribution 3.0 Unported License][ccl].

## Requirements

- [Codeigniter 2.1][ci]
- Active Record

## Setting up

To set up, is pretty straightforward.

1. Add the library (a copy or as a [submodule][git-submodule]) to the applications/core/ folder.
2. Update the `$config['subclass_prefix']` value to `EXT_` (or change the name of the core extension from `EXT_` to whatever you want)
3. Extend your models to the library (by default, to `EXT_Model`)

If using the default values, you code should be something like this:

```php
<?php

class Model extends EXT_Model {
	// code in here
}

?>
```

You would load the model as you usually do: 

```php
<?php
	$this->load->model('model');
?>
```

In your models, __define a `$_table` property with the name of the table__ so the methods can auto-load the table.

You _should_ use `$this->cdb->method()` if you're overriding the methods so you can use other database connections without issues. If you're using the default one, you can just use `$this->db` (replace `$cdb` with `$db`).

## Usage

The extension comes with some default methods:

- data()
- fetch()
- get()
- getAll()
- by()
- limit()
- insert()
- update()
- delete()
- match()

### Get data

The main methods are these two: `$this->get()` and `$this->getAll()` these are the equivalents of doing `row()` and `result()`. You will end each query with these.

You can also use `$this->fetch()` so you get the query object, _not the result_ (the equivalent of `$this->db->get()`). You'll be able to use num_rows() and other query helper methods.

### Selecting data

The `data()` method is used to fetch information from the databases. You'll pass a comma-separated string with the fields to fetch.

If we're using a model named _user_ and the table (`$this->_table`) is called _users_, we would just do:

```php
<?php
	$this->user->data('firstname, lastname, email, password')->getAll();
?>
```

This will select all the first names, last names, emails, and passwords of the users table, and return the result.

### Conditions

Using PHP magic methods, you can just pass a method with the column name and PHP automaically will call a where method for it.

Continuing the above example, if we want to select the same information, but where the username equals "Mario", we'll do:

```php
<?php
	$this->user->data('firstname, lastname, email, password')->username('Mario')->get();
?>
```

We use `get()` since it's just one result. This will generate:

```sql
	SELECT 'firstname, lastname, email, password' FROM `users` WHERE `username` = 'Mario';
```

And runs the query.

You can _chain_ methods, too. If you want to select all the data, but only where there's a certain name and last name, you'd do:

```php
<?php
	$this->user->data()->firstname('Mario')->lastname('Cuba')->get();
?>
```

This will generate:

```sql
	SELECT '*' FROM `users` WHERE `firstname` = 'Mario' AND `lastname` = 'Cuba';
```

Note that the `firstname` and `lastname` methods __do not exist__ — they are created dynamically for flexibility. Those are __database columns names__.

### Matching values

The `match()` method is used to compare a given value with the database. The method will check if the value exists in a certain field, and return a boolean value (TRUE if it exists, FALSE otherwise).

```php
<?php
	$user = 'AeroCross';
	$exists = $this->user->match($user, 'username'); // returns TRUE if the user 'AeroCross' exists in the users table
?>
```

As simple as that.

### Inserting, updating, deleting

The `insert()`, `update()` and `delete()` methods are auxiliary methods to make these operations a little bit straightforward.

Pass an asociative array containing the where clauses (field => value) and that's it.

```php
<?php
	$data = [	'username' 	=> 'AeroCross',
				'password' 	=> '123',
				'email' 	=> 'mario@mariocuba.net',
				'firstname'	=> 'Mario',
				'lastname'	=> 'Cuba'
			]; // short array syntax in 5.4 FTW!
	
	$this->user->insert($data);
?>
```
The same applies for the other methods.

### Filtering and Ordering

The `by()` and `limit()` methods are using for filtering data.

```php
<?php
	$this->user->data('username, password')->role('Admin')->by('id', 'desc')->getAll();
?>
```

Produces:

```sql
	SELECT `username`, `password` FROM `users` WHERE `role` = 'Admin' ORDER BY `id` DESC
```

If we add the limit (and optionally, the offset) clause:

```php
<?php
	$this->user->data('username, password')->role('Admin')->limit(2, 0)->by('id', 'desc')->getAll();
?>
```

```sql
	SELECT `username`, `password` FROM `users` WHERE `role` = 'Admin' ORDER BY `id` DESC LIMIT 2, 0
```

[email]: mailto:mario@mariocuba.net
[ccl]: http://creativecommons.org/licenses/by/3.0
[ci]: http://codeigniter.com
[git-submodule]: http://chrisjean.com/2009/04/20/git-submodules-adding-using-removing-and-updating/