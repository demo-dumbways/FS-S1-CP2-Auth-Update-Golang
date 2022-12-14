---
sidebar_position: 3
---

# 3. Login Page

import useBaseUrl from '@docusaurus/useBaseUrl';

Membuat halaman login kita bisa memanfaatkan code html dan css yang ada pada pertemuan sebelumnya terkait form contact me. Sehingga kita bisa berfokus pada penggunaan golang untuk mengolah data login dan memvalidasi apakah terdapat data user yang diinputkan didalam database.

<a class="btn-example-code" href="https://github.com/demo-dumbways/ebook-code-result-chapter-2-golang/blob/day6-2-login-page/views/login.html">
Contoh code 
</a>

<br />
<br />

```html title="login.html"
<html>

<head>
    <title>Login</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet"
        integrity="sha384-1BmE4kWBq78iYhFldvKuhfTAU6auU8tT94WrHftjDbrCEXSU1oBoqyl2QvZ6jIW3" crossorigin="anonymous">
    <link href="/public/style.css" rel="stylesheet">
</head>

<body>

    <!-- Navbar -->
    <nav class="navbar navbar-expand-lg navbar-light bg-light">
        <div class="container">
            <a class="navbar-brand" href="/home">
                <img src="/public/assets/logo.png">
            </a>
            <button class="navbar-toggler" type="button" data-bs-toggle="collapse"
                data-bs-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false"
                aria-label="Toggle navigation">
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
    <!-- End Navbar -->

    <!-- Form -->
    <div class="container-form-blog d-flex align-items-center justify-content-center">

        <div class="container-form">

            <p class="h2 text-center mb-4">Login</p>

            <form action="/login" method="POST">

                <div class="mb-3">
                    <label for="email" class="form-label">Email</label>
                    <input name="email" type="email" class="form-control" id="email" aria-describedby="emailHelp">
                </div>

                <div class="mb-3">
                    <label for="password" class="form-label">Password</label>
                    <input name="password" type="password" class="form-control" id="password"
                        aria-describedby="emailHelp">
                </div>


                <div class="d-flex justify-content-start">
                    <button type="submit" class="btn btn-orange text-white mt-5">Submit</button>
                </div>
            </form>

        </div>
        <!-- End Form -->
</body>

</html>
```

<img alt="image1" src={useBaseUrl('img/docs/image-6-3.png')} height="400px"/>

<br />
<br />

<div>
<a class="btn-demo" href="">
Demo
</a>
</div>
