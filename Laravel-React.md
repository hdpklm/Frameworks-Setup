# Install React in Laravel
<hr>

## Install Laravel UI
```
composer require laravel/ui
```

## Install ReactJS
```
php artisan ui react
```

## Install React with Auth
```
php artisan ui react --auth
```

## Install Some libraries (optional)
```
npm install axios react-bootstrap bootstrap 
npm install react-router react-router-dom sweetalert2 --save
npm install autoprefixer@10.4.5 --save-exact
```

## Install Required Packages
```
npm i
npm run dev
```

## Relative React Path
resources/js/components/Example.js
- create or change Examle.js
- Example.js use root div id "example" not "root" like react


## Add home page related to react
- routes/web.php `add this line`
```
Route::get('/react', function(){return view('react home blade file')]})->name('react');
```

## add home page html blade
- resources/views/react.blade.php `create file`
```html
<!DOCTYPE html>
<html lang="en">

	<head>
		<meta charset="UTF-8" />
		<link rel="icon" type="image/svg+xml" href="/vite.svg" />
		<meta name="viewport" content="width=device-width, initial-scale=1.0" />
		<title>Vite + React</title>
	</head>

	<body>
		<div id="root"></div>
		<script type="module" src="/src/main.jsx"></script>
	</body>

</html>
```












