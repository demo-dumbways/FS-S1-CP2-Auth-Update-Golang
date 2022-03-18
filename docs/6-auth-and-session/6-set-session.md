---
sidebar_position: 6
---

# 6. Set Session 

import useBaseUrl from '@docusaurus/useBaseUrl';

**Session** digunakan untuk menyimpan data semetara ke dalam variabel session itu sendiri, sehingga data yang tersimpan pada session dapat digunakan untuk keperluan tertentu, misal untuk keperluan Login. Selain itu nilai dari variable session ini dapat digunakan pada halaman mana saja `(across multiple pages)`

Ketika kita ingin menggunakan session di express.js maka kita perlu menginstall terlebih dahulu package `express-session`. Menginstall package ini akan kita lakukan menggunakan NPM.

```shell
npm install express-session
```

selanjutnya package express-session akan kita import dan gunakan didalam file entry point kita yakni index.js

```js title=index.js {3}
const bcrypt = require('bcrypt');
const flash = require('express-flash')
const session = require('express-session')

const db = require(path.join(__dirname, '../connection/db'));
```

setelah kita import package express-session, selanjutnya kita tambahkan code dibagian route `/login`untuk membuat session pada proses login

<a class="btn-example-code" href="https://github.com/demo-dumbways/ebook-code-result-chapter-2/blob/day6-7.set-session/api/index.js">
Contoh code
</a>

<br />
<br />

```js {17-32}
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
                req.session.isLogin = true
                req.session.user = {
                    id: result.rows[0].id,
                    name: result.rows[0].name,
                    email: result.rows[0].email
                }

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

<img alt="image1" src={useBaseUrl('img/docs/image-6-6.png')}/>