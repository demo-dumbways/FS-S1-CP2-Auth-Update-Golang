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

    ```go title="main.go" {23,33-45}
    // continuation this code same like before
    // this code below type Blog struct { ...........

    func main() {
        route := mux.NewRouter()

        connection.DatabaseConnect()

        // static folder
        route.PathPrefix("/public/").Handler(http.StripPrefix("/public/", http.FileServer(http.Dir("./public/"))))

        // routing
        route.HandleFunc("/", helloWorld).Methods("GET")
        route.HandleFunc("/home", home).Methods("GET").Name("home")
        route.HandleFunc("/blog", blogs).Methods("GET")
        route.HandleFunc("/blog/{id}", blogDetail).Methods("GET")
        route.HandleFunc("/add-blog", formBlog).Methods("GET")
        route.HandleFunc("/blog", addBlog).Methods("POST")
        route.HandleFunc("/delete-blog/{id}", deleteBlog).Methods("GET")

        route.HandleFunc("/contact-me", contactMe).Methods("GET")

        route.HandleFunc("/register", formRegister).Methods("GET")

        fmt.Println("Server running on port 5000")
        http.ListenAndServe("localhost:5000", route)
    }

    // .............
    // continuation this code same like before 
    // .............

    func formRegister(w http.ResponseWriter, r *http.Request) {
        w.Header().Set("Content-Type", "text/html; charset=utf-8")

        var tmpl, err = template.ParseFiles("views/register.html")
        if err != nil {
            w.WriteHeader(http.StatusInternalServerError)
            w.Write([]byte("message : " + err.Error()))
            return
        }

        w.WriteHeader(http.StatusOK)
        tmpl.Execute(w, Data)
    }

    // .............
    // continuation this code same like before 
    // .............
    ```


2.  Membuat Route untuk Menangani Proses Register

    Kita perlu membuat sebuah route untuk menangani inputan dari form register. Kita akan membuat route `/register` dengan metode `POST`.

    ```go title="main.go" {24}
    // continuation this code same like before
    // this code below type Blog struct { ...........

    func main() {
        route := mux.NewRouter()

        connection.DatabaseConnect()

        // static folder
        route.PathPrefix("/public/").Handler(http.StripPrefix("/public/", http.FileServer(http.Dir("./public/"))))

        // routing
        route.HandleFunc("/", helloWorld).Methods("GET")
        route.HandleFunc("/home", home).Methods("GET").Name("home")
        route.HandleFunc("/blog", blogs).Methods("GET")
        route.HandleFunc("/blog/{id}", blogDetail).Methods("GET")
        route.HandleFunc("/add-blog", formBlog).Methods("GET")
        route.HandleFunc("/blog", addBlog).Methods("POST")
        route.HandleFunc("/delete-blog/{id}", deleteBlog).Methods("GET")

        route.HandleFunc("/contact-me", contactMe).Methods("GET")

        route.HandleFunc("/register", formRegister).Methods("GET")
        route.HandleFunc("/register", register).Methods("POST")

        fmt.Println("Server running on port 5000")
        http.ListenAndServe("localhost:5000", route)
    }

    // .............
    // continuation this code same like before 
    // .............
    ```

    Sama dengan proses add blog post, hal pertama yang kita lakukan setelah membuat route untuk menyimpan data kedalam database. Proses menampung data inputan.

    ```go title="main.go" {5-13}    
    // .............
    // continuation this code same like before 
    // .............

    func register(w http.ResponseWriter, r *http.Request) {
        err := r.ParseForm()
        if err != nil {
            log.Fatal(err)
        }

        name := r.PostForm.Get("name")
        email := r.PostForm.Get("email")
    }

    // .............
    // continuation this code same like before 
    // .............
    ```

    password yang kita dapatkan perlu dilakukan enskripsi sebelum dimasukkan kedalam database agar aman. Maka kita perlu menggunakan `bcrypt`. **Package Bcrypt** digunakan untuk hashing kata sandi yang aman. Perbedaan utama dengan algoritma intisari biasa seperti `MD5` atau `SHA256` adalah bahwa algoritma bcrypt dirancang khusus untuk melindungi dari serangan `brute force`

    selanjutnya package bcrypt akan kita import ke file entry point kita yakni index.js

    ```go
    "golang.org/x/crypto/bcrypt"
    ```

    kita akan menggunakan `method GenerateFromPassword` yang disediakan oleh package bcrypt untuk melakukan enkripsi password yang telah kita tampung dari form register

    ```go title="main.go" {11-12} 
    // this code continue from above
    func register(w http.ResponseWriter, r *http.Request) {
        err := r.ParseForm()
        if err != nil {
            log.Fatal(err)
        }

        name := r.PostForm.Get("name")
        email := r.PostForm.Get("email")

        password := r.PostForm.Get("password")
	    passwordHash, _ := bcrypt.GenerateFromPassword([]byte(password), 10)
    }
    ```

    proses terakhir adalah kita simpan seluruh data registrasi beserta password yang telah di enkripsi kedalam database menggunakan query `INSERT`

    <a class="btn-example-code" href="">
    Contoh code
    </a>

    <br />
    <br />

    ```go title="main.go" {13-20} 
    func register(w http.ResponseWriter, r *http.Request) {
        err := r.ParseForm()
        if err != nil {
            log.Fatal(err)
        }

        name := r.PostForm.Get("name")
        email := r.PostForm.Get("email")

        password := r.PostForm.Get("password")
	    passwordHash, _ := bcrypt.GenerateFromPassword([]byte(password), 10)

        _, err = connection.Conn.Exec(context.Background(), "INSERT INTO users(name, email,password) VALUES ($1,$2,$3)", name, email, passwordHash)
        if err != nil {
            w.WriteHeader(http.StatusInternalServerError)
            w.Write([]byte("message : " + err.Error()))
            return
        }

        http.Redirect(w, r, "/login", http.StatusMovedPermanently)
    }
    ```

    <img alt="image1" src={useBaseUrl('img/docs/image-6-2.png')} height="400px"/>

    <br />
    <br />

    <div>
    <a class="btn-demo" href="">
    Demo
    </a>
    </div>