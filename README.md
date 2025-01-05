<h1 align="center">Composer</h1> <br>
<p align="center">
    <img alt="Gopher composing wires" title="Gopher" src="./assets/gopher.png" width="320">
</p>

<p align="center">
    Thin helper module for composing routers
</p>

---

## Introduction

This helper module is used for composing [go-chi](https://github.com/go-chi/chi) routers.


## How it works

A `Composer` is a struct with multiple servers and a chi mux.

```go
type Composer struct {
	servers []Server
	Mux     *chi.Mux
}
```

A `Server` is an interface with methods to return a HTTP handler and a prefix.

```go
type Server interface {
	Mux() http.Handler
	Prefix() string
}
```

Examples would be an `User` server and a `Product` server (structs that implements the `Server` interface).

```go
type UserServer struct {
	mux            *chi.Mux
	prefix         string
    // server dependencies (db, logger, etc.)
}

userServer := &UserServer{
    prefix:         "/users",
	Mux:            chi.NewRouter(),
}

type ProductServer struct {
	mux            *chi.Mux
	prefix         string
    // server dependencies (db, logger, etc.)
}

productServer := &ProductServer{
    prefix:         "/products",
	Mux:            chi.NewRouter(),
}
```

First, you create a Composer using the `NewComposer` function passing all the middlewares you want in the root router.

```go
composer := composer.NewComposer(
    middleware.Recoverer,
    middleware.CleanPath,
    middleware.RedirectSlashes,
)
```

Internally, this will create a chi router with all the middlewares received and return a pointer to a `Composer` struct.

Then, you call the `Compose` method passing all the servers to the composer (the root router).

```go
if err := composer.Compose(userServer, productServer); err != nil {
    return err
}
```

Now, you can use the `composer` as the HTTP handler.

```go
http.ListenAndServe(":8080", composer)
```


## License

