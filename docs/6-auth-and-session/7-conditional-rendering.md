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

```go title="main.go" {15-23,40-44}
// .............
// continuation this code same like before 
// .............

func blogs(w http.ResponseWriter, r *http.Request) {
	w.Header().Set("Content-Type", "text/html; charset=utf-8")

	var tmpl, err = template.ParseFiles("views/blog.html")
	if err != nil {
		w.WriteHeader(http.StatusInternalServerError)
		w.Write([]byte("message : " + err.Error()))
		return
	}

	var store = sessions.NewCookieStore([]byte("SESSION_ID"))
	session, _ := store.Get(r, "SESSION_ID")

	if session.Values["IsLogin"] != true {
		Data.IsLogin = false
	} else {
		Data.IsLogin = session.Values["IsLogin"].(bool)
		Data.UserName = session.Values["Name"].(string)
	}

	rows, _ := connection.Conn.Query(context.Background(), "SELECT id, title, image, content, post_at FROM blog ORDER BY id DESC")

	var result []Blog
	for rows.Next() {
		var each = Blog{}

		var err = rows.Scan(&each.Id, &each.Title, &each.Image, &each.Content, &each.Post_date)
		if err != nil {
			fmt.Println(err.Error())
			return
		}

		each.Author = "Ilham Fathullah"
		each.Format_date = each.Post_date.Format("2 January 2006")

		if session.Values["IsLogin"] != true {
			each.IsLogin = false
		} else {
			each.IsLogin = session.Values["IsLogin"].(bool)
		}

		result = append(result, each)
	}

	respData := map[string]interface{}{
		"Data":  Data,
		"Blogs": result,
	}

	w.WriteHeader(http.StatusOK)
	tmpl.Execute(w, respData)
}

// .............
// continuation this code same like before 
// .............
```

Selanjutnya kita akan melakukan proses pengecekan apakah pengguna memiliki session ketika mengakses halaman blog, jika memiliki session maka button akan ditampilkan

<a class="btn-example-code" href="">
Contoh code
</a>

<br />
<br />

```html title=blog.html {31-45,57-61,69-74}
<html>

<head>
  <title>{{.Data.Title}}</title>
  <link rel="stylesheet" href="/public/style.css" />
  <!-- linking boostrap css cdn  -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet"
    integrity="sha384-1BmE4kWBq78iYhFldvKuhfTAU6auU8tT94WrHftjDbrCEXSU1oBoqyl2QvZ6jIW3" crossorigin="anonymous">
</head>

<body>
  <!-- NavBar -->
  <nav class="navbar navbar-expand-lg navbar-light bg-light">
    <div class="container-lg">
      <a class="navbar-brand me-5" href="/home">
        <img src="/public/assets/logo.png" alt="logo" />
      </a>
      <div class="collapse navbar-collapse" id="navbarNav">
        <ul class="navbar-nav">
          <li class="nav-item">
            <a class="nav-link" href="/home">Home</a>
          </li>
          <li class="nav-item">
            <a href="/blog" class="nav-link list-active">Blog</a>
          </li>
        </ul>
      </div>
      <!-- add auth link -->
      <div class="d-flex pe-2 ">
        <ul class="navbar-nav">
          {{if .Data.IsLogin}}
          <li class="nav-item">
            <a href="#" class="nav-link">Hello, {{.Data.UserName}} </a>
          </li>
          <li class="nav-item">
            <a href="/logout" class="nav-link list-active">Logout</a>
          </li>
          {{else}}
          <li class="nav-item">
            <a href="/register" class="nav-link">Register</a>
          </li>
          <li class="nav-item">
            <a href="/login" class="nav-link">Login</a>
          </li>
          {{end}}
        </ul>
      </div>
      <div class="d-flex contact-me">
        <a href="/contact-me"> Contact Me </a>
      </div>
    </div>
  </nav>

  <!-- Blog list -->
  <div id="contents" class="blog-list">
    <!-- conditional post blog -->
    {{if .Data.IsLogin}}
    <div class="button-group w-100">
      <a href="/add-blog" class="btn-post">Add New Blog</a>
    </div>
    {{end}}
    <!-- dynamic content would be here -->
    {{range $index, $data := .Blogs}}
    <div class="blog-list-item">
      <div class="blog-image">
        <img src="/public/assets/blog-img.png" alt="Pasar Coding di Indonesia Dinilai Masih Menjanjikan" />
      </div>
      <div class="blog-content">
        {{if $data.IsLogin}}
        <div class="button-group">
          <a class="btn-edit">Edit Post</a>
          <a class="btn-post" href="/delete-blog/{{$data.Id}}">Delete Blog</a>
        </div>
        {{end}}
        <h1>
          <a href="/blog/{{$data.Id}}" target="_blank">
            {{$data.Title}}
          </a>
        </h1>
        <div class="detail-blog-content">
          {{$data.Format_date}} | {{$data.Author}}
        </div>
        <p>
          {{$data.Content}}
        </p>
      </div>
    </div>
    {{end}}
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