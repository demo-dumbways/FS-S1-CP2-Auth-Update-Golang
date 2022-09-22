---
sidebar_position: 5
---

# 5. Login 

import useBaseUrl from '@docusaurus/useBaseUrl';

Ketika kita ingin melakukan proses login, terdapat 2 langkah yang harus dilakukan

1.  Membuat Route View Login

    Hal pertama yang dilakukan adalah membuat route dengan metode `GET` untuk merender view form login. Route ini akan kita berikan endpoint `/login` sesuai dengan tujuannya yakni menampilkan halaman login

    <a class="btn-example-code" href="">
    Contoh code
    </a>

    <br />
    <br />

    ```go title="main.go" {26,36-48}
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

	    route.HandleFunc("/login", formLogin).Methods("GET")

        fmt.Println("Server running on port 5000")
        http.ListenAndServe("localhost:5000", route)
    }

    // .............
    // continuation this code same like before 
    // .............

    func formLogin(w http.ResponseWriter, r *http.Request) {
        w.Header().Set("Content-Type", "text/html; charset=utf-8")

        var tmpl, err = template.ParseFiles("views/login.html")
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

2.  Membuat Route untuk Menangani Proses Login

    Kita perlu membuat sebuah route untuk menangani inputan dari form login. Kita akan membuat route `/login` dengan metode `POST`.

    ```go title="main.go" {27}
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

        route.HandleFunc("/login", formLogin).Methods("GET")
	    route.HandleFunc("/login", login).Methods("POST")

        fmt.Println("Server running on port 5000")
        http.ListenAndServe("localhost:5000", route)
    }

    // .............
    // continuation this code same like before 
    // .............
    ```

    Sama dengan proses register, hal pertama yang kita lakukan setelah membuat route untuk melakukan proses logika login. Proses diawali dengan menampung data inputan.

    ```go title="main.go" {5-13}    
    // .............
    // continuation this code same like before 
    // .............

    func login(w http.ResponseWriter, r *http.Request) {
        err := r.ParseForm()
        if err != nil {
            log.Fatal(err)
        }

        email := r.PostForm.Get("email")
        password := r.PostForm.Get("password")
    }

    // .............
    // continuation this code same like before 
    // .............
    ```

    Kita akan melakukan query `SELECT` dengan tujuan mengecek apakah didalam database terdapat data yang ber emailkan sama dengan data email yang didapat dari form login. Proses ini kita lanjutkan dengan melakukan kondisi apabila tidak ada data yang ditemukan didalam database, maka akan kita tampilkan pesan error. Selain itu, kita juga menyiapkan sebuah struct untuk menampung data user dari hasil query

    ```go title="main.go" {13-20}    
    // .............
    // continuation this code same like before 
    // .............

    type User struct {
        Id       int
        Name     string
        Email    string
        Password string
    }

    // .............
    // continuation this code same like before 
    // .............

    // this code continue from above
    func login(w http.ResponseWriter, r *http.Request) {
        err := r.ParseForm()
        if err != nil {
            log.Fatal(err)
        }

        email := r.PostForm.Get("email")
        password := r.PostForm.Get("password")

        user := User{}

        err = connection.Conn.QueryRow(context.Background(), "SELECT * FROM users WHERE email=$1", email).Scan(
            &user.Id, &user.Name, &user.Email, &user.Password,
        )
        if err != nil {
            w.WriteHeader(http.StatusBadRequest)
            w.Write([]byte("message : " + err.Error()))
            return
	    }
    }
    ```

    Selanjutnya kita akan melakukan pengecekan terhadap data password didalam database dengan inputan password dari form login. Membandingkan password yang telah di enkripsi didalam database dengan inputan form login akan kita lakukan menggunakan `method CompareHashAndPassword`. Apabila bernilai `true` yang artinya sama data didalam database dengan inputan maka akan kita arahkan ke halaman utama. 
    
    ```go title="main.go" {22-27}    
    // this code continue from above
    func login(w http.ResponseWriter, r *http.Request) {
        err := r.ParseForm()
        if err != nil {
            log.Fatal(err)
        }

        email := r.PostForm.Get("email")
        password := r.PostForm.Get("password")

        user := User{}

        err = connection.Conn.QueryRow(context.Background(), "SELECT * FROM users WHERE email=$1", email).Scan(
            &user.Id, &user.Name, &user.Email, &user.Password,
        )
        if err != nil {
            w.WriteHeader(http.StatusBadRequest)
            w.Write([]byte("message : " + err.Error()))
            return
	    }

        err = bcrypt.CompareHashAndPassword([]byte(user.Password), []byte(password))
        if err != nil {
            w.WriteHeader(http.StatusBadRequest)
            w.Write([]byte("message : " + err.Error()))
            return
        }
    }
    ``` 

    Terakhir kita akan mengirimkan pesan bahwa berhasil melakukan proses login menggunakan flash message apabila hasil pengecekan password bernilai `true`. Maka kita membutuhkan package `gorilla/sessions` yang akan kita install. **gorilla/sessions** adalah sebuah package yang memungkinkan kita untuk mengirimkan pesan dari backend ke frontend

    Maka kita perlu meng install package gorilla/session terlebih dahulu menggunakan command berikut

    ```shell
    go get -u github.com/gorilla/sessions
    ```
    <br/>

    <a class="btn-example-code" href="">
    Contoh code
    </a>

    <br />
    <br />

    ```go title="main.go" {29-35}    
    // this code continue from above
    func login(w http.ResponseWriter, r *http.Request) {
        err := r.ParseForm()
        if err != nil {
            log.Fatal(err)
        }

        email := r.PostForm.Get("email")
        password := r.PostForm.Get("password")

        user := User{}

        err = connection.Conn.QueryRow(context.Background(), "SELECT * FROM users WHERE email=$1", email).Scan(
            &user.Id, &user.Name, &user.Email, &user.Password,
        )
        if err != nil {
            w.WriteHeader(http.StatusBadRequest)
            w.Write([]byte("message : " + err.Error()))
            return
	    }

        err = bcrypt.CompareHashAndPassword([]byte(user.Password), []byte(password))
        if err != nil {
            w.WriteHeader(http.StatusBadRequest)
            w.Write([]byte("message : " + err.Error()))
            return
        }

        var store = sessions.NewCookieStore([]byte("SESSION_ID"))
	    session, _ := store.Get(r, "SESSION_ID")

        session.AddFlash("Login success", "message")
        session.Save(r, w)

        http.Redirect(w, r, "/home", http.StatusMovedPermanently)
    }
    ``` 

    <img alt="image1" src={useBaseUrl('img/docs/image-6-3.png')} height="400px"/>

    <br />
    <br />

    <div>
    <a class="btn-demo" href="">
    Demo
    </a>
    </div>
