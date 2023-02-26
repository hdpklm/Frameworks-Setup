# create laravel project
```
composer create-project laravel/laravel project_name
cd project_name 
```

# php artisan commands
> run server in local
  ```
  php artisan serve
  ```

> create database migration and Model
  ```
  php artisan make:model [ModelName] --m
  php artisan make:model [ModelName] --migration
  ```

## after change .env
  ```sh
  php artisan optimize
  php artisan cache:clear
  php artisan config:clear
  php artisan route:clear
  ```