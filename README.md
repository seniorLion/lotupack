# Ход сборки  
### Инициализация нового проекта:  
`npm init`
### Установка Gulp на диск (глобально):  
`npm i gulp-cli -g`
### Установка Gulp в проект (app/srs):  
`npm i gulp -D`
### Создаем рабочие папки с файлами:  
`app, dist, css > style.scss, js > main.js`
### Подключаем в index.html файлы css и js:  
`<link rel="stylesheet" href="css/style.min.css">`  
`<script src="js/main.min.js"></script>`
### Создаем переменную с названием {src, dest} и подключаем возможности 'gulp':  
`const {src, dest} = require('gulp');`
### Подключаем плагин (препроцессор) Sass в сборку:  
`npm i gulp-sass sass -D`
### Создаем переменную для подключения Sass:  
`const scss = require('gulp-sass')(require('sass'));`
### Пишем функцию для работы с стилями:
>`function styles() {`  
`return src('app/css/style.scss')`  
`.pipe(scss())`  
`.pipe(dest('app/css'))`  
`}`
### Экспортируем функцию `styles` для её использования в терминале:  
`exports.styles = styles;`
### Обновляем функцию `styles` добавив сжатие кода `css`:
>`function styles() {`  
`return src('app/css/style.scss')`  
`.pipe(scss(`***{outputStyle: 'compressed'}***  `))`  
`.pipe(dest('app/css'))`  
`}`
### Проверяем написанную функцию:  
`gulp style`
### Подключаем плагин Concat:  
`npm i gulp-concat -D`
### Создаем переменную для плагина Concat:  
`const concat = require('gulp-concat');`
### Обновляем функцию `styles` с использованием плагина Concat:
>`function styles() {`  
`return src('app/css/style.scss')`  
***.pipe(concat('style.min.css'))***  
`.pipe(scss({outputStyle: 'compressed'}))`  
`.pipe(dest('app/css'))`  
`}`
### Устанавливаем плагин для сжатия кода JS:  
`npm i gulp-uglify-es -D`
### Создаем переменную для плагина Uglify:  
`const uglify = require('gulp-uglify-es').default;`
### Пишем функцию для работы с Javascript:
>`function scripts() {`  
`return src('app/js/main.js')`  
`.pipe(concat('main.min.js'))`  
`.pipe(uglify())`  
`.pipe(dest('app/js'))`  
`}`
### Экспортируем функцию `scripts` для её использования в терминале:  
`exports.scripts = scripts;`
### Проверяем написанную функцию:  
`gulp scripts`
### Добавляем функцию Watch:  
`const {src, dest, `***watch***`} = require('gulp');`  
>`function watching() {`  
  `watch(['app/css/style.scss'], styles)`  
  `watch(['app/js/main.js'], scripts)`  
`}`  

`exports.watching = watching;`
### Устанавливаем плагин для авто-обновления браузера:
`npm i browser-sync`  
`const browserSync = require('browser-sync').create();`  
>`function browsersync() {`  
  `browserSync.init({`  
    `server: {`  
        `baseDir: "app/"`  
    `}`  
`});`  
`}`

Обновляем функцию styles и scripts: `.pipe(browserSync.stream())`  
`exports.browsersync = browsersync;`
### Обновляем функцию `watching` для слежения за файлами `html`:
`watch(["app/*.html"]).on('change', browserSync.reload);`
### Добавляем `parallel` и создаем `gulp.default`:
`const {src, dest, watch, parallel} = require('gulp');`  
`exports.default = parallel(styles, scripts, browsersync, watching);`
### Устанавливаем плагин для поддержки старых браузеров:
`npm i gulp-autoprefixer -D`  
`const autoprefixer = require('gulp-autoprefixer');`  
Обновляем функцию *styles*:  
`.pipe(autoprefixer({overrideBrowserslist: ['last 10 version']}))`
### Создаем функцию *building* для перемещения готовых файлов в папку *dist*:
>`function building() {`  
  `return src([`  
    `'app/css/style.min.css',`  
    `'app/js/main.min.js',`  
    `'app/**/*.html'`  
  `], {base: 'app'})`  
  `.pipe(dest('dist'))`  
`}`
### Устанавливаем *gulp-clean* для перезаписи и очистки папки *dist*:
`npm i gulp-clean`  
`const clean = require('gulp-clean');`  
>`function cleanDist() {`  
  `return src('dist')`  
    `.pipe(clean())`  
`}`
### Создаем *build*:
`exports.build = series(cleanDist, building);`
## Часть 2
### Подключаем плагины:
`gulp-avif`  
`gulp-cached`  
`gulp-imagemin`  
`gulp-webp`
### Пишем функцию *images*:
>`function images() {`  
  `return src(['app/images/src/*.*', '!app/images/src/*.svg'])`  
  `.pipe(avif({quality: 50}))`  
  `.pipe(src('app/images/src/*.*'))`  
  `.pipe(webp())`  
  `.pipe(src('app/images/src/*.*'))`  
  `.pipe(imagemin())`  
  `.pipe(dest('app/images/dist'))`  
`}`

`exports.images = images;`