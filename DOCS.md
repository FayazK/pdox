# PDOx Documentation

## Install
composer.json file:
```json
{
    "require": {
        "izniburak/pdox": "dev-master"
    }
}
```
after run the install command.
```
$ composer install
```

OR run the following command directly.

```
$ composer require izniburak/pdox:dev-master
```


## Quick Usage
```php
require 'vendor/autoload.php';

$config = [
	'host'      => 'localhost',
	'driver'    => 'mysql',
	'database'  => 'test',
	'username'  => 'root',
	'password'  => '',
	'charset'   => 'utf8',
	'collation' => 'utf8_general_ci',
	'prefix'    => ''
];

$db = new \Buki\Pdox($config);
```


Congratulations! Now, you can use PDOx.

If you have a problem, you can [contact me][support-url].




# Detailed Usage and Methods

## config 
```php
$config = [
	# Host name or IP Address (not required)
	# default value: localhost
	'host'      => 'localhost',
		
	# Database Driver Type (not required)
	# default value: mysql
	# values: mysql, pgsql, sqlite, oracle
	'driver'    => 'mysql',
		 
	# Database Name (required)
	'database'  => 'test',
		
	# Database User Name (required)
	'username'  => 'root',
		
	# Database User Password (required)
	'password'  => '',
		
	# Database Charset (not required)
	# default value: utf8
	'charset'   => 'utf8',
		
	# Database Charset Collation (not required)
	# default value: utf8_general_ci
	'collation' => 'utf8_general_ci',
		
	# Database Prefix (not required)
	# default value: null
	'prefix'     => '',
	
	# Cache Directory of the Sql Result
	# default value: __DIR__ . '/cache/'
	'cachedir'	=> __DIR__ . '/cache/sql/'
];

$db = new \Buki\Pdox($config);
```

### select
```php
# Usage 1: string parameter
$db->select('title, content');
$db->select('title AS t, content AS c');

# Usage2: array parameter
$db->select(['title', 'content']);
$db->select(['title AS t', 'content AS c']);
```

### table
```php
# Usage 1: string parameter
$db->table('table');
$db->table('table1, table2');
$db->table('table1 AS t1, table2 AS t2');

# Usage2: array parameter
$db->table(['table1', 'table2']);
$db->table(['table1 AS t1', 'table2 AS t2']);
```

### get AND getAll
```php
# get(): return 1 record.
# getAll(): return multiple records.

$db->table('test')->getAll(); 	// " SELECT * FROM test "
$db->select('username')->table('users')->where('status', 1)->getAll(); 	// " SELECT username FROM users WHERE status = '1' "

$db->select('title')->table('pages')->where('id', 17)->get(); // " SELECT title FROM pages WHERE id = '17' LIMIT 1 "
```

### join
```php
# Usage 1: 
$db->table('foo')->join('bar', 'foo.field', 'bar.field')->getAll();
$db->table('foo')->leftJoin('bar', 'foo.field', 'bar.field')->getAll();
$db->table('foo')->rightJoin('bar', 'foo.field', 'bar.field')->get();
$db->table('foo')->innerJoin('bar', 'foo.field', 'bar.field')->get();

# Usage 2:
$db->table('foo')->join('bar', 'foo.field', '=', 'bar.field')->getAll();
$db->table('foo')->leftJoin('bar', 'foo.field', '=', 'bar.field')->getAll();
$db->table('foo')->rightJoin('bar', 'foo.field', '=', 'bar.field')->get();
$db->table('foo')->innerJoin('bar', 'foo.field', '=', 'bar.field')->get();

# Usage 3:
$db->table('foo')->join(['left outer', 'bar', 'foo.field = bar.field'])->getAll();
$db->table('foo')->join(['right outer', 'bar', 'foo.field', 'bar.field'])->get();
```

### where - orWhere
```php
# Usage 1: array parameter
$where = [
	'name' => 'Burak',
	'age' => 23,
	'status' => 1
];
$db->where($where);

# Usage 2: 
$db->where('status', 2);
$db->where('status', 1)->where('name', 'burak');
$db->where('status', 1)->orWhere('status', 2);

# Usage 3:
$db->where('age', '>', 20);
$db->where('age', '>', 20)->orWhere('age', '<', 30);

# Usage 4: 
$db->where('status = ? AND age = ?', [1, 20]);
$db->where('status = ? AND title = ?', [0, 'example title']);
```

### grouped
```php
$db->table('users')
	->grouped(function() use ($db) {
		$db->where('country', 'TURKEY')->orWhere('country', 'ENGLAND');
	})
	->where('status', 1)
	->getAll();
```

### in - notIn - orIn - orNotIn
```php
$db->in('page', ['about', 'contact', 'products']);
$db->orIn('id', [1, 2, 3]);
$db->notIn('age', [20, 21, 22, 23]);
$db->orNotIn('age', [30, 31, 32, 32]);
```

### between - orBetween - notBetween - orNotBetween
```php
$db->between('age', 10, 20);
$db->orBetween('age', 20, 30);
$db->notBetween('year', 2010, 2015);
$db->orNotBetween('year', 2005, 2009);
```

### like - orLike
```php
$db->like('title', 'burak');		// " title LIKE '%burak%' "
$db->like('title', 'izniburak', '-%');	// " title LIKE 'izniburak%' "
$db->like('title', 'izniburak', '%-');	// " title LIKE '%izniburak' "

$db->like('user', 'php')->orLike('user', 'web');
$db->like('user', 'php')->orLike('user', 'web', '-%');
$db->like('user', 'php')->orLike('user', 'web', '%-');
```

### groupBy
```php
# Usage 1: string parameter
$db->groupBy('country');
$db->groupBy('country, city');

# Usage 2: array parameter
$db->groupBy(['country', 'city']);
```

### having
```php
$db->having('AVG(price)', 2000);	// " AVG(price) > 2000 "
$db->having('AVG(price)', '>=', 3000);	// " AVG(price) >= 3000 "
$db->having('SUM(age) <= ?', [50]);	// " SUM(age) <= 50 "
```

### orderBy
```php
$db->orderBy('id');	// " ORDER BY id ASC
$db->orderBy('id DESC');
$db->orderBy('id', 'desc');
$db->orderBy('rand()');	// " ORDER BY rand() "
```

### limit
```php
$db->limit(10);		// " LIMIT 10 "
$db->limit(10, 20);	// " LIMIT 10, 20 "
```

### insert
```php
$data = [
	'title' => 'test',
	'content' => 'Lorem ipsum dolor sit amet...',
	'time' => time(),
	'status' => 1
];

$db->table('pages')->insert($data);
```

### update
```php
$data = [
	'username' => 'izniburak',
	'password' => md5('demo-password'),
	'activation' => 1,
	'status' => 1
];

$db->table('users')->where('id', 10)->update($data);
```

### delete
```php
$db->table('users')->where('id', 5)->delete();
```

### query
```php
$db->query('SELECT * FROM test WHERE id = ? AND status = ?', [10, 1]);
```

### insertId
```php
$data = [
	'title' => 'test',
	'content' => 'Lorem ipsum dolor sit amet...',
	'time' => time(),
	'status' => 1
];

$db->table('pages')->insert($data);

var_dump($db->insertId());
```

### count
```php
$db->select('id, title')->table('test')->where('status', 1)->orWhere('status', 2)->getAll();

var_dump($db->count());
```

### error
```php
$db->error();
```

### cache
```php
# Usage: ...->cache($time)->... 
$db->table('pages')->where('slug', 'example-page.html')->cache(60)->get(); // cache time: 60 seconds
```

### queryCount
```php
$db->queryCount(); // The number of all SQL queries on the page until the end of the beginning.
```

### getQuery
```php
$db->getQuery(); // Last SQL Query.
```

### escape
```php

```

[support-url]: https://github.com/izniburak/PDOx#support
