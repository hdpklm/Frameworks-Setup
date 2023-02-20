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
npm run watch
```

## Relative React Path
resources/js/components/Example.js
- create or change Examle.js
- Example.js use root div id "example" not "root" like react
- `resources/js/app.js` have config initial file in react

## split React js and Vue js files
- for react dir: disable `require('./react/app.js');` in `resources/js/app.js`
- for default dir: disable `require('./components/app.js');` in `resources/js/app.js`
- modify `webpack.mix.js`
```js
const mix = require('laravel-mix');
mix
	.js('resources/js/app.js', 'public/js')
	.js('resources/js/react/app.js', 'public/js/react.js') // <-- add this line, it will create new file for react-js 
	.react()
	.sass('resources/sass/app.scss', 'public/css')
	.version(); // <--- this is for change version at every build
```

## Add home page related to react
- routes/web.php `add this line`
```php
Route::get('/react', function(){return view('react');})->name('react');
```

## change initial react file
- resources/js/components/Example.js
```jsx
import React from 'react';
import ReactDOM from 'react-dom';

function Example() {
	return (<div className="card-header">Example Component</div>);
}

export default Example;

if (document.getElementById('root')) {
	ReactDOM.render(<Example />, document.getElementById('root'));
}
```

## add home page html blade
- resources/views/react.blade.php `create file`
```html
<!DOCTYPE html>
<html lang="en">
	<head>
		<meta charset="UTF-8" />
		<link rel="icon" type="image/svg+xml" href="{{ asset('logo.ico') }}" />
		<meta name="viewport" content="width=device-width, initial-scale=1.0" />
		<title>Vite + React</title>
	</head>

	<body>
		<div id="root"></div>
		<!-- [mix] is use for add hash at every build -->
		<script type="module" src="{{ mix('/js/react.js') }}"></script>
	</body>
</html>
```












