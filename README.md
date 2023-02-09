# Gulp 4

- **Instal Gulp 4 in project**

```shell
npm i gulp --save-dev
```

- **Define Gulp constants in `gulpfile.js`**

```javascript
const { src, dest, parallel, series, watch } = require('gulp');
```

- **Installing  Live Server**

```shell
npm i browser-sync --save-dev
```

- **Connecting Browsersync, creat it logic work and export**

```javascript
const browserSync = require('browser-sync').create();

function browsersync() {
browserSync.init({ // Initializing Browsersync
	server: { baseDir: 'app/' }, // Specify the server folder
	notify: false, // Turning off notifications
	online: true // Mode of operation: true or false
  })
}

exports.browsersync = browsersync;
```

- **Installing and connecting moduls `gulp-concat` `gulp-uglify-es`**

``` shell
npm i gulp-concat gulp-uglify-es --save-dev
```

```javascript
const concat = require('gulp-concat')
const uglify = require('gulp-uglify-es').default
```
- **Creatting function script and export it**

```javascript
function scripts() {
	return src([ // Taking files from sources
		'node_modules/jquery/dist/jquery.min.js', // Library connection example
		'app/js/app.js', // Custom scripts that use the library must be connected at the end of the
		])
	.pipe(concat('app.min.js')) // Concatinating into one file
	.pipe(uglify()) // Compressing JavaScript
	.pipe(dest('app/js/')) // Upload the finished file to the destination folder
	.pipe(browserSync.stream()) // Trigger Browsersync to refresh the page
}

	exports.scripts = scripts;
```

- **Creatting function fo work with preprocessors and export it**
```javascript

let preprocessors = "sass"// Choose preprocessor in the project - sass or less

function styles() {
  return src(`app/${preprocessor}/main.${preprocessor}`)
    .pipe(eval(preprocessor)())
    .pipe(concat("app.min.css"))
    .pipe(
      autoprefixer({ overrideBrowserslist: ["last 10 versions"], grid: true })
    )
    .pipe(
      cleanss({ level: { 1: { specialComments: 0 } } /* format: "beautify" */ })
    )//Minimizing Styles
    .pipe(dest("app/css/"))
    .pipe(browserSync.stream());
}

exports.styles = styles;
```
- **Creatting function fo work with images and export it**

```shell
npm i gulp-imagemin@7.1.0
npm install gulp-newer --save-dev
```
```javascript
function images() {
  return src("app/images/src/**/*")
    .pipe(newer("app/images/dest/"))
    .pipe(imagemin())
    .pipe(dest("app/images/dest/"));
}

exports.images = images;
```


- **Create a function that will make current copy of the project**

```javascript
function buildcopy() {
  return src(
    [
      "app/css/**/*.min.css",
      "app/js/**/*.min.js",
      "app/images/dest/**/*",
      "app/**/*.html",
    ],
    { base: "app" } //The parameter "base" preserves the structure of the project when copying
  ).pipe(dest("dist")); 
}
```

- **Create a function that will watch after the condition of the projec project**

```javascript 
function startwatch() {
  watch(`app/**/${preprocessor}/**/*`, styles);
  watch(["app/**/*.js", "!app/**/*.min.js"], scripts);
  watch("app/**/*.html").on("change", browserSync.reload);
  watch("app/images/src/**/*", images);
}
```

- **Create export for correctly starting live server**

```javascript
exports.default = parallel(scripts, styles, browsersync, startwatch);
```

- **Create export for correctly building project in folder dist**

_For start creating function cleaning folder dist_

```shell
npm i del@6.1.1
```

```javasctipt
async function cleandist() {
  return del("dist/**/*"), { force: true };
}
```

```javasctipt
exports.build = series(cleandist, styles, scripts, images, buildcopy);
```

---
**How to make it work?**

Install the dependencies:
```shell
npm install
```
Running a web server:
```shell
gulp 
```
Build:
```shell
gulp build
```




