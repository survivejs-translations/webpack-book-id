# apa itu webpack

Webpack adalah **module bundler**. Webpack dapat menangani bundling di samping menjalankan tugas yang terpisah, namun, garis antara tugas bundler dan runner telah menjadi hilang berkat plugin webpack yang dikembangkan oleh komunitas. Terkadang plugin ini digunakan untuk melakukan tugas yang biasanya dilakukan di luar webpack, seperti membersihkan direktori build atau deploying build.

dengan React **Hot Module Replacement** (HMR) membantu mempopulerkan webpack dan menyebabkan di tempat lain menggunakan webpack, seperti [Ruby on Rails](https://github.com/rails/webpacker). Meski namanya, webpack tidak terbatas pada web saja. Ini bisa dibundel untuk target lain juga, seperti dibahas di bab *Build Targets* chapter.

T> Jika Anda ingin memahami pembuatan alat ini dan sejarah mereka dengan lebih rinci, lihatlah *Comparison of Build Tools* lampiran.

## modul reliese di webpack

Proyek terkecil yang bisa Anda bundel dengan webpack terdiri dari **masuk** and **keluar**. Proses bundling dimulai dari **entri**. yang di tentukan pengguna **modul** dan bisa menunjuk ke modul lain melalui **import**.

Saat Anda mengemas proyek menggunakan webpack, Ini terlintasi melalui impor, membangun **grafik dependensi** dari proyek dan kemudian menghasilkan ** output **  yang berdasarkan konfigurasi. Selain itu, memungkinkan untuk menggabungkan **poin terpisah** menghasilkan kumpulan yang terpisah dari kode proyek itu sendiri.

Webpack mendukung format modul ES2015, CommonJS, dan AMD di luar kotak. mekanisme berkerja loader untuk CSS juga, dengan `@import` dan `url()` dukungan dari *css-loader*. Anda juga bisa menemukan plugin untuk tugas-tugas tertentu, seperti minification, internasionalation, HMR, dan sebagainya.

T> Grafik dependensi adalah graf yang berarah menggambarkan bagaimana berhubungan satu simpul sama lain. Dalam hal ini definisi grafik didefinisikan melalui referensi(`require`, `import`) antar file. melalui Webpack informasi ini secara statis tanpa mengeksekusi sumber untuk menghasilkan grafik yang dibutuhkan untuk membuat kumpulan.

## Proses Eksekusi Webpack

![Webpack's execution process](images/webpack-process.png)

kerja webpack dimulai dari **entri**. Seringkali ini adalah modul JavaScript memulai proses  webpack traversalnya. Selama proses ini, webpack mengevaluasi kecocokan masuk dengan **loader** konfigurasi yang memberi tahu bagaimana mengubah webpack di setiap pertandingan.

{pagebreak}

### Proses Resolusi

Entri itu sendiri adalah sebuah modul. Ketika webpack menemukan satu, webpack mencoba mencocokkan dengan entri sistem file dengan menggunakan konfigurasi `resolve` entri. Anda bisa memberi tahu webpack untuk melakukan pencarian terhadap direktori tertentu selain  node_modules*. Ini juga memungkinkan untuk menyesuaikan cara mencocokkan webpack dengan ekstensi file, dan Anda dapat menentukan aliases khusus untuk direktori. T*Teknik Mengkonsumsi Paket* mencakup gagasan ini secara lebih rinci.

Jika resolusi berlalu gagal, webpack menimbulkan kesalahan runtime. Jika webpack berhasil menyelesaikan sebuah file dengan benar, webpack melakukan pemrosesan melalui file yang sesuai berdasarkan definisi loader. Setiap loader menerapkan transformasi spesifik terhadap isi modul.

Cara loader mencocokkan dengan file yang diselesaikan dapat dikonfigurasi dengan berbagai cara, termasuk oleh jenis file dan berdasarkan lokasi dalam sistem file. Fleksibilitas Webpack, bahkan memungkinkan Anda menerapkan transformasi spesifik terhadap file berdasarkan *di mana* diimpor ke proyek.

Proses resolusi yang sama dilakukan terhadap webpack. Webpack Anda memungkinkan  menerapkan logika yang sama saat menentukan loader mana yang harus digunakan. Loader telah yang menyelesaikan alasan konfigurasi mereka sendiri untuk  ini. Jika webpack gagal melakukan pencarian loader, itu akan meningkatkan kesalahan runtime.

### Webpack Mengatasi Setiap Jenis File

Webpack akan menyelesaikan setiap modul yang ditemui saat membuat grafik dependensi. Jika entri berisi dependensi, proses akan dilakukan secara rekursif terhadap setiap dependensi sampai traversal selesai. Webpack dapat melakukan proses ini terhadap jenis file apa pun, tidak seperti alat khusus seperti kompiler Babel atau Sass.

Webpack memberi Anda kendali atas bagaimana memperlakukan berbagai aset yang ditemukannya. Sebagai contoh, Anda dapat memutuskan untuk **menyamakan** aset dengan kumpulan JavaScript Anda untuk menghindari permintaan. Webpack juga memungkinkan Anda menggunakan teknik seperti Modul CSS untuk beberapa gaya dengan komponen, dan untuk menghindari masalah gaya CSS standar. Fleksibilitas inilah yang membuat webpack begitu berharga.

Meskipun webpack digunakan terutama untuk menggabungkan JavaScript, ia dapat juga menangkap aset seperti gambar atau tulisan dan memancarkan file terpisah untuk mereka. Entri hanya merupakan titik awal dari proses bundling. Apa webpack yang dipancarkan sepenuhnya tergantung pada cara Anda mengkonfigurasinya.

### Proses evaluasi

Dengan asumsi semua loader ditemukan, webpack mengevaluasi loader yang sesuai dari kiri ke kanan dan kanan ke kiri(`styleLoader(cssLoader('./main.css'))`), menjalankan modul melalui setiap loader secara bergantian. akibatnya, Anda mendapatkan output yang akan disisipkan webpack dalam paket **yang dihasilkan**. *Loader Definitions* mencakup judul topik secara rinci.

Jika semua evaluasi loader selesai tanpa kesalahan runtime, webpack memasukkan sumber dalam bundel terakhir. ** Plugin** memungkinkan Anda untuk mencegat ** acara runtime** pada tahap yang berbeda dari proses bundling.

Meskipun loader dapat melakukan banyak hal, mereka tidak menyediakan cukup tenaga untuk tugas selanjutan. Plugin dapat mencegat **acara runtime** yang disediakan oleh webpack. Contoh yang baik adalah ekstraksi bundel yang dilakukan oleh `ExtractTextPlugin` yang bisa digunakan dengan loader, ekstrak file CSS dari bundel dan ke dalam file terpisah. Tanpa langkah ini, CSS akan dimasukkan ke dalam JavaScript yang dihasilkan, karena webpack memperlakukan semua kode sebagai JavaScript secara default. tehnik ekstraksi dibahas di bab *Memisahkan CSS*.

### penyelesaian

Setelah setiap modul dievaluasi, webpack menulis **output**. Outputnya memasukkan skrip bootstrap dengan manifes yang menjelaskan bagaimana cara mulai mengeksekusi hasilnya di browser. Manifes dapat diekstraksi ke file miliknya sendiri, seperti yang akan dibahas nanti di buku ini. Outputnya berbeda berdasarkan target yang Anda gunakan (penargetan web bukan satu-satunya pilihan).

Itu tidak semua ada pada proses bundling. Misalnya, Anda dapat menentukan **poin split** tertentu dimana webpack menghasilkan kumpulan terpisah yang dimuatkan berdasarkan logika aplikasi. Ide ini dibahas di bab *Code Splitting*.

{pagebreak}

## Webpack Is Configuration Driven

At its core, webpack relies on configuration. Here is a sample configuration adapted from [the official webpack tutorial](https://webpack.js.org/get-started/) that covers the main points:

**webpack.config.js**

```javascript
const webpack = require("webpack");

module.exports = {
  // Where to start bundling
  entry: {
    app: "./entry.js",
  },

  // Where to output
  output: {
    // Output to the same directory
    path: __dirname,

    // Capture name from the entry using a pattern
    filename: "[name].js",
  },

  // How to resolve encountered imports
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ["style-loader", "css-loader"],
      },
      {
        test: /\.js$/,
        use: "babel-loader",
        exclude: /node_modules/,
      },
    ],
  },

  // What extra processing to perform
  plugins: [
    new webpack.optimize.UglifyJsPlugin(),
  ],

  // Adjust module resolution algorithm
  resolve: {
    alias: { ... },
  },
};
```

Webpack's configuration model can feel a bit opaque at times as the configuration file can appear monolithic. It can be difficult to understand what webpack is doing unless you understand the ideas behind it. Providing means to tame configuration is one of the main purposes why this book exists.

T> To understand webpack on the source code level, check out [the artsy webpack tour](https://github.com/TheLarkInn/artsy-webpack-tour).

## Asset Hashing

With webpack, you can inject a hash to each bundle name (e.g., *app.d587bbd6.js*) to invalidate bundles on the client side as changes are made. Bundle-splitting allows the client to reload only a small part of the data in the ideal case.

{pagebreak}

## Hot Module Replacement

You are likely familiar with tools, such as [LiveReload](http://livereload.com/) or [BrowserSync](http://www.browsersync.io/), already. These tools refresh the browser automatically as you make changes. *Hot Module Replacement* (HMR) takes things one step further. In the case of React, it allows the application to maintain its state without forcing a refresh. While this does not sound all that special, it can make a big difference in practice.

HMR is also available in Browserify via [livereactload](https://github.com/milankinen/livereactload), so it’s not a webpack exclusive feature.

## Code Splitting

In addition to HMR, webpack’s bundling capabilities are extensive. Webpack allows you to split code in various ways. You can even load code dynamically as your application gets executed. This sort of lazy loading comes in handy especially for larger applications, as dependencies can be loaded on the fly as needed.

Even small applications can benefit from code splitting, as it allows the users to get something useable in their hands faster. Performance is a feature, after all. Knowing the basic techniques is worthwhile.

{pagebreak}

## Conclusion

Webpack comes with a significant learning curve. However, it’s a tool worth learning, given how much time and effort it can save over the long term. To get a better idea how it compares to other tools, check out [the official comparison](https://webpack.js.org/get-started/why-webpack/#comparison).

Webpack won’t solve everything, however, it does solve the problem of bundling. That’s one less worry during development. Using *package.json* and webpack alone can take you far.

To summarize:

* Webpack is a **module bundler**, but you can also use it for task running as well.
* Webpack relies on a **dependency graph** underneath. Webpack traverses through the source to construct the graph, and it uses this information and configuration to generate bundles.
* Webpack relies on **loaders** and **plugins**. Loaders operate on a module level, while plugins rely on hooks provided by webpack and have the best access to its execution process.
* Webpack’s **configuration** describes how to transform assets of the graphs and what kind of output it should generate. Part of this information can be included in the source itself if features like **code splitting** are used.
* **Hot Module Replacement** (HMR) helped to popularize webpack. It's a feature that can enhance the development experience by updating code in the browser without needing a full page refresh.
* Webpack can generate **hashes** for filenames allowing you to invalidate past bundles as their contents change.

In the next part of the book you'll learn to construct a development configuration using webpack while learning more about its basic concepts.
