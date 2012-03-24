# Codeigniter Core Model Extension

A simple core extension library for Codeigniter that allows simple and flexible database queries using Method Chaining.

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

``php
<?php

class Model extends EXT_Model {
	// code in here
}
?>
``

You would load the model as you usually do: `$this->load->model('model'); ?>`. In your models, __define a $_table property with the name of the table__ so the methods can auto-load the table.

You _should_ use `$this->cdb->method()` if you're overriding the methods so you can use other database connections without issues. If you're using the default one, you can just use $this->db (replace $cdb with $db).

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

If we're using a model named _user_ and the table (`$this->_table`)is called _users_, we would just do:

	$this->user->data('firstname, lastname, email, password')->getAll();

This will select all the first names, last names, emails, and passwords of the users table, and return the result.

### Conditions

Using PHP magic methods, you can just pass a method with the column name and PHP automaically will call a where method for it.

Continuing the above example, if we want to select the same information, but where the username equals "Mario", we'll do:

	$this->user->data('firstname, lastname, email, password')->username('Mario')->get();

We use `get()` since it's just one result. This will generate:

	SELECT 'firstname, lastname, email, password' FROM `users` WHERE `username` = 'Mario';

And runs the query.

[ccl]: http://creativecommons.org/licenses/by/3.0
[ci]: http://codeigniter.com
[git-submodule]: http://chrisjean.com/2009/04/20/git-submodules-adding-using-removing-and-updating/