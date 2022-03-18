---
sidebar_position: 4
---

# 4. Register & Encrypt Password

import useBaseUrl from '@docusaurus/useBaseUrl';

Ketika kita ingin melakukan proses registrasi, terdapat 2 langkah yang harus dilakukan

1.  Membuat Route View Register

    Hal pertama yang dilakukan adalah membuat route dengan metode `GET` untuk merender view form register. Route ini akan kita berikan endpoint `/register` sesuai dengan tujuannya yakni menampilkan halaman register

    <a class="btn-example-code" href="https://github.com/demo-dumbways/ebook-code-result-chapter-2/blob/day6-3.get-register/api/index.js">
    Contoh code
    </a>

    <br />
    <br />

    ```js title=index.js {6-9}
    app.get('/contact-me', function (req, res) {
        setHeader(res)
        res.render('contact')
    })

    app.get('/register', function (req, res) {
        setHeader(res)
        res.render('register')
    })

    const port = 5000
    app.listen(port, function () {
        console.debug(`Server running on port ${port}`)
    })
    ```


2.  Membuat Route untuk Menangani Proses Register

    Kita perlu membuat sebuah route untuk menangani inputan dari form register. Kita akan membuat route `/register` dengan metode `POST`.

    ```js
    app.post('/register', function(req, res){
    
    })
    ```

    Sama dengan proses add blog post, hal pertama yang kita lakukan setelah membuat route untuk menyimpan data kedalam database. Proses menampung data inputan, kita akan melakukan dengan cara `object destructuring` .

    ```js {2}
    app.post('/register', function(req, res){
        const { email, name, password } = req.body
    })
    ```

    password yang kita dapatkan perlu dilakukan enskripsi sebelum dimasukkan kedalam database agar aman. Maka kita perlu menginstal `package bcrypt` meggunakan NPM. **Package Bcrypt** digunakan untuk hashing kata sandi yang aman. Perbedaan utama dengan algoritma intisari biasa seperti `MD5` atau `SHA256` adalah bahwa algoritma bcrypt dirancang khusus untuk melindungi dari serangan `brute force`

    ```shell
    npm install bcrypt
    ```

    selanjutnya package bcrypt akan kita import ke file entry point kita yakni index.js

    ```js
    const bcrypt = require('bcrypt')
    ```

    kita akan menggunakan `method hashSync` yang disediakan oleh package bcrypt untuk melakukan enkripsi password yang telah kita tampung dari form register

    ```js {4}
    app.post('/register', function(req, res){
        const { email, name, password } = req.body

        const hashedPassword = bcrypt.hashSync(password, 10)
    })
    ```

    proses terakhir adalah kita simpan seluruh data registrasi beserta password yang telah di enkripsi kedalam database menggunakan query `INSERT`

    <a class="btn-example-code" href="https://github.com/demo-dumbways/ebook-code-result-chapter-2/blob/day6-4.post-register/api/index.js">
    Contoh code
    </a>

    <br />
    <br />

    ```js {6-21}
    app.get('/register', function (req, res) {
        setHeader(res)
        res.render('register')
    })

    app.post('/register', function (req, res) {
        const { email, name, password } = req.body

        const hashedPassword = bcrypt.hashSync(password, 10)

        let query = `INSERT INTO tb_user(name, email, password) VALUES('${name}', '${email}', '${hashedPassword}')`

        db.connect(function (err, client, done) {
            if (err) throw err

            client.query(query, function (err, result) {
                if (err) throw err
                res.redirect('/login')
            })
        })
    })

    const port = 5000
    app.listen(port, function () {
        console.debug(`Server running on port ${port}`)
    })
    ```

    <img alt="image1" src={useBaseUrl('img/docs/image-6-2.png')} height="400px"/>

    <br />
    <br />

    <div>
    <a class="btn-demo" href="https://personal-web-chapter-2.herokuapp.com/register">
    Demo
    </a>
    </div>