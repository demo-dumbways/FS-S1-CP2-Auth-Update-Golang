---
sidebar_position: 5
---

# 5. Login 

import useBaseUrl from '@docusaurus/useBaseUrl';

Ketika kita ingin melakukan proses login, terdapat 2 langkah yang harus dilakukan

1.  Membuat Route View Login

    Hal pertama yang dilakukan adalah membuat route dengan metode `GET` untuk merender view form login. Route ini akan kita berikan endpoint `/login` sesuai dengan tujuannya yakni menampilkan halaman login

    <a class="btn-example-code" href="https://github.com/demo-dumbways/ebook-code-result-chapter-2/blob/day6-5.get-login/api/index.js">
    Contoh code
    </a>

    <br />
    <br />

    ```js title=index.js {3-6}
    // this code below endpoint app.post('/register',  .....

    app.get('/login', function (req, res) {
        setHeader(res)
        res.render('login')
    })

    const port = 5000
    app.listen(port, function () {
        console.debug(`Server running on port ${port}`)
    })
    ```

2.  Membuat Route untuk Menangani Proses Login

    Kita perlu membuat sebuah route untuk menangani inputan dari form login. Kita akan membuat route `/login` dengan metode `POST`.

    ```js
    app.post('/login', function(req, res){
    
    })
    ```

    Sama dengan proses register, hal pertama yang kita lakukan setelah membuat route untuk melakukan proses logikan login. Proses diawali dengan menampung data inputan, yang akan kita lakukan menggunakan cara `object destructuring` .

    ```js {2}
    app.post('/login', function(req, res){
        const { email, password } = req.body
    })
    ```

    Pada proses login nantinya akan terdapat proses menampilkan pesan error ketika tidak ditemukan data yang cocok antara inputan dengan database. Maka kita membutuhkan package `express-flash` yang akan kita install melalui NPM. **Express-flash** adalah sebuah package yang memungkinkan kita untuk mengirimkan pesan dari backend ke frontend.

    ```shell
    npm install express-flash
    ```

    selanjutnya package express-flash akan kita import dan gunakan didalam file entry point kita yakni index.js

    ```js title=index.js {2,8}
    const bcrypt = require('bcrypt');
    const flash = require('express-flash')

    const db = require(path.join(__dirname, '../connection/db'));

    const app = express()

    app.use(flash())

    app.set('view engine', 'hbs');
    app.set("views", path.join(__dirname, "../views"));
    ```

    Selanjutnya kita akan melakukan query `SELECT` dengan tujuan mengecek apakah didalam database terdapat data yang ber emailkan sama dengan data email yang didapat dari form login. Proses ini kita lanjutkan dengan melakukan kondisi apabila tidak ada data yang ditemukan didalam database, maka akan kita tampilkan pesan error dan arahkan kembali ke tampilan login.

    ```js {4-17}
    app.post('/login', function(req, res){
        const { email, password } = req.body

        let query = `SELECT * FROM tb_user WHERE email = '${email}'`

        db.connect(function(err,client,done){
            if (err) throw err

            client.query(query, function(err,result){
                if (err) throw err

                if(result.rows.length == 0){
                    req.flash('danger',"Email & Password don't match!")
                    return res.redirect('/login')
                }
            })
        })
    })
    ```

    terakhir kita akan melakukan pengecekan terhadap data password didalam database dengan inputan password dari form login. Membandingkan password yang telah di enkripsi didalam database dengan inputan form login akan kita lakukan menggunakan `method compareSync`. Apabila bernilai `true` yang artinya sama data didalam database dengan inputan maka akan kita arahkan ke halaman utama study case, jika tidak maka akan kita arahkan kedalam tampilan login lagi beserta pesan error.

    <a class="btn-example-code" href="https://github.com/demo-dumbways/ebook-code-result-chapter-2/blob/day6-6.post-login/api/index.js">
    Contoh code
    </a>

    <br />
    <br />

    ```js {17-25} title=index.js
    app.post('/login', function(req, res){
        const { email, password } = req.body

        let query = `SELECT * FROM tb_user WHERE email = '${email}'`

        db.connect(function(err,client,done){
            if (err) throw err

            client.query(query, function(err,result){
                if (err) throw err

                if(result.rows.length == 0){
                    req.flash('danger',"Email & Password don't match!")
                    return res.redirect('/login')
                }

                let isMatch = bcrypt.compareSync(password, result.rows[0].password)

                if(isMatch){
                    req.flash('success', "Login success")
                    res.redirect('/blog')
                } else {
                    req.flash('danger', "Email & Password don't match!")
                    res.redirect('/login')
                }
            })
        })
    })
    ```    

    <img alt="image1" src={useBaseUrl('img/docs/image-6-3.png')} height="400px"/>

    <br />
    <br />

    <div>
    <a class="btn-demo" href="https://personal-web-chapter-2.herokuapp.com/login">
    Demo
    </a>
    </div>
