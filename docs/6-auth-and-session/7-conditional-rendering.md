---
sidebar_position: 7
---

# 7. Conditional Rendering Button

import useBaseUrl from '@docusaurus/useBaseUrl';

Conditional rendering adalah proses merender/menampilkan komponen berdasarkan kondisi -  kondisi tertentu. Pada case kali ini kita akan melakukan conditional rendering terhadap button `Add Post Blog` berdasarkan session. Sehingga button akan tampil ketika pengguna sudah memiliki session atau sudah melakukan login.

Hal pertama yang kita lakukan adalah mengirimkan `data session` saat proses render halaman blog melalui route `/blog`. Hal ini bertujuan agar nantinya dapat mengecek apakah terdapat data session atau tidak pada saat mengakses halaman blog.

<a class="btn-example-code" href="https://github.com/demo-dumbways/ebook-code-result-chapter-2/blob/day6-8.conditional-rendering/api/index.js">
Contoh code
</a>

<br />
<br />

```js {23}
// this code below endpoint app.get('/home' .....
app.get('/blog', function (req, res) {
  db.connect(function(err, client, done){
    if (err) throw err
      
      let query = "SELECT tb_blog.id, tb_blog.title, tb_blog.content, tb_blog.post_date, name FROM tb_blog, tb_user WHERE tb_user.id = tb_blog.author"

        client.query(query, function(err,result){
        if (err) throw err
            let data = result.rows

            data = data.map(function(blog){
                return {
                    ...blog,
                    post_at: getFullTime(blog.post_at),
                    post_age: getDistanceTime(blog.post_at)
                }
            })

            res.render('blog', 
                { 
                    blogs: data,
                    isLogin: req.session.isLogin,
                    user: req.session.user
                })
        })
    })
})
```

Selanjutnya kita akan melakukan proses pengecekan apakah pengguna memiliki session ketika mengakses halaman blog, jika memiliki session maka button akan ditampilkan

<a class="btn-example-code" href="https://github.com/demo-dumbways/ebook-code-result-chapter-2/blob/day6-8.conditional-rendering/views/blog.hbs">
Contoh code
</a>

<br />
<br />

```html title=blog.hbs {50-54}
<html>

<head>
  <title>Creating Blog Page</title>
  <link rel="stylesheet" href="/public/style.css" />
  <!-- linking boostrap css cdn  -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet"
    integrity="sha384-1BmE4kWBq78iYhFldvKuhfTAU6auU8tT94WrHftjDbrCEXSU1oBoqyl2QvZ6jIW3" crossorigin="anonymous">
</head>

<body>
  <!-- NavBar -->
  <nav class="navbar navbar-expand-lg navbar-light bg-light">
    <div class="container">
      <a class="navbar-brand" href="/home">
        <img src="public/assets/logo.png">
      </a>
      <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent"
        aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>

      <div class="collapse navbar-collapse" id="navbarSupportedContent">
        <ul class="navbar-nav me-auto mb-2 mb-lg-0">
          <li class="nav-item">
            <a class="nav-link active" aria-current="page" href="/home">Home</a>
          </li>
          <li class="nav-item">
            <a class="nav-link active" aria-current="page" href="/blog">Blog</a>
          </li>
        </ul>

        <ul class="navbar-nav ms-auto mb-2 mb-lg-0">
          <li class="nav-item">
            <a class="nav-link active  text-orange" aria-current="page" href="/register">Register</a>
          </li>
          <li class="nav-item">
            <a class="nav-link active" aria-current="page" href="/login">Login</a>
          </li>
        </ul>

        <a href="/contact-me" class="btn btn-orange text-white px-4 ms-5">Contact Me</a>
      </div>
    </div>
  </nav>

  <!-- Blog list -->
  <div id="contents" class="blog-list">
    <!-- conditional post blog -->
    {{#if isLogin}}
    <div class="button-group w-100">
      <a href="/add-blog" class="btn-post">Add New Blog</a>
    </div>
    {{/if}}
    <!-- dynamic content would be here -->
    <!-- using each expression to iterate blogs data sent -->
    {{#each blogs}}
    <div class="blog-list-item">
      <div class="blog-image">
        <img src="/public/assets/blog-img.png" alt="Pasar Coding di Indonesia Dinilai Masih Menjanjikan" />
      </div>
      <div class="blog-content">
        <div class="button-group">
          <a href="/update-blog/{{this.id}}" class="btn-edit">Edit Post</a>
          <a href="/delete-blog/{{this.id}}" class="btn-post">Delete Blog</a>
        </div>
        <h1>
          <a href="/blog/{{this.id}}" target="_blank">{{this.title}}</a>
        </h1>
        <div class="detail-blog-content">
          {{this.post_date}} | {{this.author}}
        </div>
        <p>{{this.content}}</p>
      </div>
    </div>
    {{/each}}
  </div>
</body>

</html>
```

<img alt="image1" src={useBaseUrl('img/docs/image-6-7-2.png')} height="400px"/>

<br />
<br />

<div>
<a class="btn-demo" href="https://personal-web-chapter-2.herokuapp.com/blog">
Demo
</a>
</div>