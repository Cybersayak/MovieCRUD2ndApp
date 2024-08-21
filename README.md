# MovieCRUD2ndApp

### This is my second app of learning golang 

This repository contains a simple CRUD (Create, Read, Update, Delete) API for a movie application built using Golang. The API allows you to manage a collection of movies, each with an ID, ISBN, title, and director information.

## Table of Contents

- [Installation](#installation)
- [Usage](#usage)
- [API Endpoints](#api-endpoints)
- [Code Explanation](#code-explanation)

## Installation

1. Clone the repository:
    ```sh
    git clone https://github.com/Cybersayak/MovieCRUD2ndApp.git
    ```
2. Navigate to the project directory:
    ```sh
    cd MovieCRUD2ndApp
    ```
3. Install the required dependencies:
    ```sh
    go get -u github.com/gorilla/mux
    ```
4. Run the application:
    ```sh
    go run main.go
    ```

## Usage

Once the server is running, you can interact with the API using tools like `curl` or Postman. The server will be running on `http://localhost:8080`.

## API Endpoints

- **GET /movies**: Retrieve all movies.
- **GET /movies/{id}**: Retrieve a specific movie by ID.
- **POST /movies**: Create a new movie.
- **PUT /movies/{id}**: Update an existing movie by ID.
- **DELETE /movies/{id}**: Delete a movie by ID.

## Code Explanation

The main code for the API is in the `main.go` file. Below is an explanation of the key parts of the code:

### Imports and Struct Definitions

```go
import (
	"encoding/json"
	"fmt"
	"github.com/gorilla/mux"
	"log"
	"math/rand"
	"net/http"
	"strconv"
)

type Movie struct {
	ID       string    `json:"id"`
	Isbn     string    `json:"isbn"`
	Title    string    `json:"title"`
	Director *Director `json:"director"`
}

type Director struct {
	FirstName string `json:"firstname"`
	LastName  string `json:"lastname"`
}
```

- The necessary packages are imported.
- Two structs, `Movie` and `Director`, are defined to represent the movie data.

### Global Variables and Handlers

```go
var movies []Movie

func getMovies(w http.ResponseWriter, r *http.Request) {
	w.Header().Set("Content-Type", "application/json")
	json.NewEncoder(w).Encode(movies)
}

func deleteMovie(w http.ResponseWriter, r *http.Request) {
	w.Header().Set("Content-Type", "application/json")
	params := mux.Vars(r)
	for index, item := range movies {
		if item.ID == params["id"] {
			movies = append(movies[:index], movies[index+1:]...)
			break
		}
	}
	json.NewEncoder(w).Encode(movies)
}

func getMovie(w http.ResponseWriter, r *http.Request) {
	w.Header().Set("Content-Type", "application/json")
	params := mux.Vars(r)
	for _, item := range movies {
		if item.ID == params["id"] {
			json.NewEncoder(w).Encode(item)
			return
		}
	}
}

func createMovie(w http.ResponseWriter, r *http.Request) {
	w.Header().Set("Content-Type", "application/json")
	var movie Movie
	_ = json.NewDecoder(r.Body).Decode(&movie)
	movie.ID = strconv.Itoa(rand.Intn(100000000))
	movies = append(movies, movie)
	json.NewEncoder(w).Encode(movie)
}

func updateMovie(w http.ResponseWriter, r *http.Request) {
	w.Header().Set("Content-Type", "application/json")
	params := mux.Vars(r)
	for index, item := range movies {
		if item.ID == params["id"] {
			movies = append(movies[:index], movies[index+1:]...)
			var movie Movie
			_ = json.NewDecoder(r.Body).Decode(&movie)
			movie.ID = params["id"]
			movies = append(movies, movie)
			json.NewEncoder(w).Encode(movie)
		}
	}
}
```

- `movies` is a global variable that stores the list of movies.
- The handler functions (`getMovies`, `deleteMovie`, `getMovie`, `createMovie`, `updateMovie`) manage the CRUD operations.

### Main Function

```go
func main() {
	r := mux.NewRouter()

	movies = append(movies, Movie{ID: "1", Isbn: "111-1111", Title: "Movie 1", Director: &Director{FirstName: "Rituporno", LastName: "Ghosh"}})
	movies = append(movies, Movie{ID: "2", Isbn: "111-1234", Title: "Movie 2", Director: &Director{FirstName: "Sayak", LastName: "Ghosh"}})

	r.HandleFunc("/movies", getMovies).Methods("GET")
	r.HandleFunc("/movies/{id}", getMovie).Methods("GET")
	r.HandleFunc("/movies", createMovie).Methods("POST")
	r.HandleFunc("/movies/{id}", updateMovie).Methods("PUT")
	r.HandleFunc("/movies/{id}", deleteMovie).Methods("DELETE")

	fmt.Println("Starting server on port 8080\n")
	log.Fatal(http.ListenAndServe(":8080", r))
}
```

- The `main` function initializes the router and sets up the initial movie data.
- The routes are defined and associated with their respective handler functions.
- The server is started on port 8080.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
