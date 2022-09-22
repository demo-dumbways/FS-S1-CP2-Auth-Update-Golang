---
sidebar_position: 6
---

# 6. Set Session 

import useBaseUrl from '@docusaurus/useBaseUrl';

**Session** digunakan untuk menyimpan data semetara ke dalam variabel session itu sendiri, sehingga data yang tersimpan pada session dapat digunakan untuk keperluan tertentu, misal untuk keperluan Login. Selain itu nilai dari variable session ini dapat digunakan pada halaman mana saja `(across multiple pages)`

pastikan package gorilla/session sudah kita import dan gunakan didalam file entry point kita yakni main.go

setelah kita import package gorilla/session, selanjutnya kita tambahkan code dibagian route `/login`untuk membuat session pada proses login

<a class="btn-example-code" href="">
Contoh code
</a>

<br />
<br />

 ```go title="main.go" {32-34}    
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
        
        session.Values["IsLogin"] = true
	    session.Values["Name"] = user.Name
	    session.Options.MaxAge = 10800 

        session.AddFlash("Login success", "message")
        session.Save(r, w)

        http.Redirect(w, r, "/home", http.StatusMovedPermanently)
    }
``` 

<img alt="image1" src={useBaseUrl('img/docs/image-6-6.png')}/>