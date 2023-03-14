# create laravel project
```
composer create-project laravel/laravel project_name
cd project_name 
```

## php artisan commands
> run server in local
  ```
  php artisan serve
  ```

> create database migration and Model
  ```
  php artisan make:model [ModelName] --m
  php artisan make:model [ModelName] --migration
  ```

## build and create tables in database
> before migrate db at first time, you must change this file for prevent this error
  ```sql
  SQLSTATE[42000]: Syntax error or access violation: 1071 Specified key was too long; max key length is 1000 bytes
  ```
> file `config\database.php`
  ```php
  change value of return connections.mysql.engine from null to "InnoDB"
  ```
  
> now you can migrate all tables to database
  ```sh
  php artisan migrate
  ```
  
## after change .env
  ```sh
  php artisan optimize:clear
  
  # or 
  php artisan optimize
  php artisan cache:clear
  php artisan config:clear
  php artisan route:clear
  ```
