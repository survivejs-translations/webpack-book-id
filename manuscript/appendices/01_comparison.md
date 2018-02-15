# Comparison of Build Tool

Kembali di hari, itu sudah cukup untuk menggabungkan skrip bersama-sama. Waktu telah berubah, meskipun, dan sekarang mendistribusikan kode JavaScript Anda bisa menjadi usaha yang rumit. Masalah ini semakin meningkat seiring dengan single page applications (SPAs). Mereka cenderung mengandalkan banyak perpustakaan besar dan kuat.

Untuk alasan ini, Ada beberapa strategi tentang cara memuatnya. Anda bisa memuat semuanya sekaligus atau mempertimbangkan memuatkan pustaka sesuai kebutuhan Anda. Webpack mendukung banyak strategi semacam ini.

Popularitas Node [npm](https://www.npmjs.com/), manajer paketnya, memberikan konteks yang lebih. Sebelum npm menjadi populer, sulit untuk mengkonsumsi dependensi. Ada periode ketika orang mengembangkan manajer paket khusus frontend, Tapi npm menang di akhir. Kini manajemen ketergantungan lebih mudah dari sebelumnya, meskipun masih ada tantangan yang harus diatasi.

## Task Runners and Bundlers

berbicara Secara historis, Ada banyak build tools. *make* Mungkin yang paling dikenal, dan itu masih merupakan pilihan yang tepat. Khusus *task runners*, seperti Grunt dan Gulp diciptakan terutama dengan pengembang JavaScript. Plugin yang tersedia melalui npm membuat kedua pelari tugas bertenaga dan bisa diperpanjang. Mungkin untuk menggunakan bahkan npm `scripts` sebagai task runner. Itu umum, terutama dengan webpack.

Task runners adalah tools yang hebat pada tingkat tinggi. Mereka memungkinkan Anda melakukan operasi secara cross-platform. Masalahnya dimulai saat Anda perlu menyatukan berbagai aset sekaligus menghasilkan bundel. *bundlers*, seperti Browserify, Brunch, atau webpack, ada karena alasan ini.

{pagebreak}

Untuk sementara, [RequireJS](http://requirejs.org/) sangat populer. Idenya adalah untuk memberikan definisi modul asynchronous dan membangun di atas itu. Formatnya, AMD, dibahas lebih rinci nanti di bab ini. Untungnya, standar telah berhasil menyusul, dan RequireJS nampaknya lebih seperti keingintahuan saat ini.
## Make

[Make](https://en.wikipedia.org/wiki/Make_%28software%29) berjalan kembali, seperti yang awalnya dirilis pada tahun 1977. Meskipun itu alat lama, tetap relevan. Jadikan Anda bisa menulis tugas terpisah untuk berbagai keperluan. Misalnya, Anda bisa memiliki tugas yang berbeda untuk membuat pembuatan produk, meminimalkan JavaScript atau menjalankan tes Anda. Anda bisa menemukan ide yang sama di banyak alat lainnya.

Meskipun Make banyak digunakan dengan proyek C, ini tidak terkait dengan C dengan cara apa pun. James Coglan membahas secara rinci [how to use Make with JavaScript](https://blog.jcoglan.com/2014/02/05/building-javascript-projects-with-make/). Pertimbangkan kode disingkat berdasarkan postingan James di bawah ini:

**Makefile**

```makefile
PATH  := node_modules/.bin:$(PATH)
SHELL := /bin/bash

source_files := $(wildcard lib/*.coffee)
build_files  := $(source_files:%.coffee=build/%.js)
app_bundle   := build/app.js
spec_coffee  := $(wildcard spec/*.coffee)
spec_js      := $(spec_coffee:%.coffee=build/%.js)

libraries    := vendor/jquery.js

.PHONY: all clean test

all: $(app_bundle)

build/%.js: %.coffee
    coffee -co $(dir $@) $<

$(app_bundle): $(libraries) $(build_files)
    uglifyjs -cmo $@ $^

test: $(app_bundle) $(spec_js)
    phantomjs phantom.js

clean:
    rm -rf build
```

Dengan Make, Anda memodelkan tugas Anda menggunakan perintah sintaks dan perintah Make-specific sehingga memungkinkan untuk mengintegrasikan dengan webpack.

## RequireJS

[RequireJS](http://requirejs.org/) barangkali merupakan pemuat naskah pertama yang menjadi sangat populer. Ini memberi tampilan yang pertama pada JavaScript modular di web. Daya tarik terbesarnya adalah AMD. diperkenalkan `define` sampul:

```javascript
define(["./MyModule.js"], function (MyModule) {
  return function() {}; // Export at module root
});

// or
define(["./MyModule.js"], function (MyModule) {
  return {
    hello: function() {...}, // Export as a module function
  };
});
```

{pagebreak}

Kebetulan, itu mungkin untuk menggunakan `require` dalam sampul:

```javascript
define(["require"], function (require) {
  var MyModule = require("./MyModule.js");

  return function() {...};
});
```

Pendekatan terakhir ini menghilangkan bagian dari kekacauan. Anda masih berakhir dengan kode yang terasa berlebihan. ES2015 dan standar lain yang memecahkan ini.

T> Jamund Ferguson telah menulis sebuah rangkaian blog yang sangat bagus tentang bagaimana cara port dari [RequireJS to webpack](https://gist.github.com/xjamundx/b1c800e9282e16a6a18e).

## npm `scripts` menggukan sebuah Task Runner

Meskipun npm CLI tidak terutama dirancang untuk digunakan sebagai task runner, tetap bekerja dan terimasih kepada *package.json* bidang `scripts`. Perhatikan contoh di bawah ini:

**package.json**

```json
"scripts": {
  "stats": "webpack --env production --json > stats.json",
  "start": "webpack-dev-server --env development",
  "deploy": "gh-pages -d build",
  "build": "webpack --env production"
},
```

Skrip ini bisa didaftarkan menggunakan `npm run` dan kemudian dieksekusi dengan menggunakan `npm run <script>`. Anda juga bisa menentukan namespace skrip Anda menggunakan konvensi seperti `test:watch`. Masalah dengan pendekatan ini adalah perlunya tetap mempertahankan cross-platform.

sebagai gantinya `rm -rf`, Anda mungkin ingin menggunakan utilitas seperti [rimraf](https://www.npmjs.com/package/rimraf) dan seterusnya. Ada kemungkinan untuk meminta pelari tugas lainnya di sini untuk menyembunyikan fakta bahwa Anda menggunakannya. Dengan cara ini Anda bisa melakukan refactor tooling Anda sekaligus menjaga antarmuka tetap sama.

## Grunt

![Grunt](images/grunt.png)

[Grunt](http://gruntjs.com/) adalah task runner yang pertama untuk perkembangan frontend. Arsitektur pluginnya berkontribusi terhadap popularitasnya. Plugin sering rumit sendiri. Akibatnya, ketika konfigurasi tumbuh, bisa menjadi sulit untuk memahami apa yang sedang terjadi.

Inilah contoh dari [Grunt documentation](http://gruntjs.com/sample-gruntfile). Dalam konfigurasi ini, Anda mendefinisikan linting dan watcher tasks. pada saat *watch* task dijalankan, menimbulkan *lint* begitujuga dengan task. Dengan cara ini, saat Anda menjalankan Grunt, Anda mendapat peringatan secara real-time di terminal saat Anda mengedit kode sumber.

{pagebreak}

**Gruntfile.js**

```javascript
module.exports = grunt => {
  grunt.initConfig({
    lint: {
      files: ["Gruntfile.js", "src/**/*.js", "test/**/*.js"],
      options: {
        globals: {
          jQuery: true,
        },
      },
    },
    watch: {
      files: ["<%= lint.files %>"],
      tasks: ["lint"],
    },
  });

  grunt.loadNpmTasks("grunt-contrib-jshint");
  grunt.loadNpmTasks("grunt-contrib-watch");

  grunt.registerTask("default", ["lint"]);
};
```

Dalam prakteknya, Anda akan memiliki banyak tugas kecil untuk tujuan tertentu, seperti membangun proyek. Bagian penting dari kekuatan Grunt adalah menyembunyikan banyak kabel dari Anda.

Diambil terlalu jauh, ini bisa menjadi masalah. Ini bisa menjadi sulit untuk memahami apa yang terjadi di bawah tenda. Itulah pelajaran arsitektur yang bisa diambil dari Grunt.

T> [grunt-webpack](https://www.npmjs.com/package/grunt-webpack) Plugin memungkinkan Anda menggunakan webpack di lingkungan Grunt saat Anda meninggalkan pengangkatan berat ke webpack.

## Gulp

![Gulp](images/gulp.png)

[Gulp](http://gulpjs.com/) mengambil pendekatan yang berbeda. Alih-alih mengandalkan konfigurasi per-plugin, Anda menangani kode aktual. Jika Anda terbiasa dengan Unix dan pigping, Anda akan menyukai Gulp. Anda memiliki *sources* untuk mencocokkan file, *filters* untuk mengoperasikan sumber-sumber ini, dan *sinks* untuk pipa hasil membangun.

Berikut adalah contoh singkat *Gulpfile* yang diadaptasi dari README proyek untuk memberi Anda gagasan yang lebih baik mengenai pendekatan ini:

**Gulpfile.js**

```javascript
const gulp = require("gulp");
const coffee = require("gulp-coffee");
const concat = require("gulp-concat");
const uglify = require("gulp-uglify");
const sourcemaps = require("gulp-sourcemaps");
const del = require("del");

const paths = {
  scripts: ["client/js/**/*.coffee", "!client/external/**/*.coffee"],
};

// Not all tasks need to use streams.
// A gulpfile is another node program
// and you can use all packages available on npm.
gulp.task("clean", () => del(["build"]));
gulp.task("scripts", ["clean"], () =>
  // Minify and copy all JavaScript (except vendor scripts)
  // with sourcemaps all the way down.
  gulp
    .src(paths.scripts)
    // Pipeline within pipeline
    .pipe(sourcemaps.init())
    .pipe(coffee())
    .pipe(uglify())
    .pipe(concat("all.min.js"))
    .pipe(sourcemaps.write())
    .pipe(gulp.dest("build/js"))
);
gulp.task("watch", () => gulp.watch(paths.scripts, ["scripts"]));

// The default task (called when you run `gulp` from CLI).
gulp.task("default", ["watch", "scripts"]);
```

Mengingat konfigurasi adalah kode, Anda selalu bisa meng-hacknya jika mengalami masalah. Anda bisa membungkus paket Node yang ada sebagai plugin Gulp, dan seterusnya. Dibandingkan dengan Grunt, Anda memiliki gagasan yang lebih jelas tentang apa yang sedang terjadi. Anda masih akan banyak menulis boilerplate untuk tugas santai. Di situlah pendekatan yang lebih baru masuk.

T> [webpack-stream](https://www.npmjs.com/package/webpack-stream) memungkinkan Anda untuk menggunakan webpack di lingkungan Gulp.

{pagebreak}

## Browserify

![Browserify](images/browserify.png)

Berurusan dengan modul JavaScript selalu menjadi masalah. Bahasa itu sendiri tidak memiliki konsep modul sampai ES2015. kemudian, bahasa itu terjebak di tahun 90-an ketika datang ke lingkungan browser. Berbagai solusi, termasuk [AMD](http://requirejs.org/docs/whyamd.html), telah di usulkan.

[Browserify](http://browserify.org/) adalah salah satu solusi untuk masalah modul. Hal ini memungkinkan modul CommonJS digabungkan bersama. Anda bisa menghubungkannya dengan Gulp, dan Anda dapat menemukan alat transformasi yang lebih kecil yang memungkinkan Anda bergerak melampaui penggunaan dasar. sebagai contoh, [watchify](https://www.npmjs.com/package/watchify) menyediakan pengamat file yang membuat kumpulan untuk Anda selama usaha penghematan pembangunan.

Ekosistem Browserify terdiri dari banyak modul kecil. Dengan cara ini, Browserify menganut filosofi Unix. Browserify lebih mudah diadopsi daripada webpack, dan ternyata merupakan alternatif yang baik untuknya.

T> [Splittable](https://www.npmjs.com/package/splittable) adalah sampul Browserify yang memungkinkan pemisahan kode, mendukung ES2015 di luar kotak, getaran pohon, dan banyak lagi.

T> [transform-loader](https://www.npmjs.com/package/transform-loader) memungkinkan Anda untuk menggunakan Browserify berubah dengan webpack.

## JSPM

![JSPM](images/jspm.png)

menggunakan [JSPM](http://jspm.io/) sangat berbeda dari alat sebelumnya. Muncul dengan tool command line sendiri yang digunakan untuk menginstal paket baru ke proyek, membuat bundel produksi, dan seterusnya. Ini mendukung [SystemJS plugins](https://github.com/systemjs/systemjs#plugins) yang memungkinkan Anda memuat berbagai format ke proyek Anda.

## Brunch

![Brunch](images/brunch.png)

Dibandingkan Gulp, [Brunch](http://brunch.io/) beroperasi pada tingkat abstraksi yang lebih tinggi. Ini menggunakan pendekatan deklaratif yang serupa dengan paket webpack. Sebagai contoh, pertimbangkan konfigurasi berikut yang disesuaikan dari situs Brunch:

```javascript
module.exports = {
  files: {
    javascripts: {
      joinTo: {
        "vendor.js": /^(?!app)/,
        "app.js": /^app/,
      },
    },
    stylesheets: {
      joinTo: "app.css",
    },
  },
  plugins: {
    babel: {
      presets: [react", "env"],
    },
    postcss: {
      processors: [require("autoprefixer")],
    },
  },
};
```

Brunch datang dengan perintah seperti `brunch new`, `brunch watch --server`, dan `brunch build --production`. Ini berisi banyak di luar kotak dan dapat diperpanjang dengan menggunakan plugin.

T> Ada yang eksperimental [Hot Module Reloading runtime](https://www.npmjs.com/package/hmr-brunch) untuk Brunch.

## Webpack

![webpack](images/webpack.png)

bisa disebutkan dengan [webpack](https://webpack.js.org/) mengambil pendekatan yang lebih monolitik daripada Browserify. Sedangkan Browserify terdiri dari beberapa alat kecil, webpack hadir dengan inti yang menyediakan banyak fungsi di luar kotak.

Inti webpack dapat diperpanjang dengan menggunakan spesifik *loaders* dan *plugins*. Ini memberi kontrol atas bagaimana *resolves* modul, sehingga memungkinkan untuk menyesuaikan bangunan Anda agar sesuai dengan situasi dan paket pemecahan masalah tertentu yang tidak bekerja dengan benar di luar kotak.

Dibandingkan dengan alat lainnya, webpack hadir dengan kompleksitas awal, namun ini membuat ini melalui rangkaian fitur yang luas. Ini alat canggih yang membutuhkan kesabaran. Tapi begitu Anda memahami ide dasar di baliknya, webpack menjadi sangat kuat.

## Other Options

Anda dapat menemukan lebih banyak alternatif seperti yang tercantum di bawah ini:

* [pundle](https://www.npmjs.com/package/pundle) mengiklankan dirinya sebagai bundler generasi berikutnya dan mencatat terutama kinerjanya.
* [Rollup](https://www.npmjs.com/package/rollup) berfokus pada bundling kode ES2015. *Tree shaking* adalah salah satu nilai jualnya. Anda bisa menggunakan Rollup dengan webpack melalui [rollup-loader](https://www.npmjs.com/package/rollup-loader).
* [AssetGraph](https://www.npmjs.com/package/assetgraph) mengambil pendekatan yang sama sekali berbeda dan dibangun di atas semantik HTML sehingga ideal untuk [hyperlink analysis](https://www.npmjs.com/package/hyperlink) atau [structural analysis](https://www.npmjs.com/package/assetviz). [webpack-assetgraph-plugin](https://www.npmjs.com/package/webpack-assetgraph-plugin)  bersama jembatan webpack dan AssetGraph.
* [FuseBox](https://www.npmjs.com/package/fuse-box) adalah bundler yang berfokus pada kecepatan. Ini menggunakan pendekatan zero-configuration dan bertujuan untuk dapat digunakan di luar kotak.
* [StealJS](https://stealjs.com/) adalah loader ketergantungan dan alat bantu yang berfokus pada kinerja dan kemudahan penggunaan.
* [Flipbox](https://www.npmjs.com/package/flipbox)membungkus beberapa bundel di belakang antarmuka yang seragam.
* [Blendid](https://www.npmjs.com/package/blendid)adalah perpaduan Gulp dan bundlers untuk membentuk jaringan aset.

## Conclusion

Secara historis ada banyak alat buat untuk JavaScript. Masing-masing telah mencoba memecahkan masalah tertentu dengan caranya sendiri. Standar sudah mulai mengejar ketinggalan dan sedikit usaha diperlukan seputar semantik dasar. Sebagai gantinya, alat dapat bersaing pada tingkat yang lebih tinggi dan mendorong pengalaman pengguna yang lebih baik. Seringkali Anda bisa menggunakan beberapa solusi terpisah bersama-sama.

rekapan:

* **Task runners** dan **bundlers** memecahkan masalah yang berbeda Anda bisa mencapai hasil yang sama dengan keduanya tapi seringkali yang terbaik adalah menggunakannya bersama untuk saling melengkapi.
* Older tools, seperti Make atau RequireJS, masih memiliki pengaruh meskipun mereka tidak begitu populer dalam pengembangan web seperti dulu.
* Bundlers seperti Browserify atau webpack memecahkan masalah penting dan membantu Anda mengelola aplikasi web yang kompleks.
* Sejumlah teknologi yang muncul mendekati masalah dari berbagai sudut. Terkadang mereka membangun di atas alat lain dan terkadang bisa digunakan bersamaan.
