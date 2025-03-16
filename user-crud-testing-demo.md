Here's a complete POC (Proof-of-Concept) demonstrating CRUD operations for a User entity using the Gin web framework, GORM (Go ORM), and comprehensive test cases with explanations.

### Project Structure:
```
user-crud-gin-gorm
├── main.go
├── models
│   └── user.go
├── controllers
│   └── user_controller.go
├── routes
│   └── routes.go
├── tests
│   └── user_controller_test.go
└── go.mod
```

---

### Step-by-step Implementation:

### **1. Initialize Go Module:**
Run this command in your project directory:
```bash
go mod init user-crud-gin-gorm
```

### **2. Install Dependencies:**
```bash
go get github.com/gin-gonic/gin gorm.io/gorm gorm.io/driver/sqlite
```

---

### **3. `models/user.go`:**
```go
package models

import (
	"gorm.io/gorm"
)

type User struct {
	gorm.Model
	Name  string `json:"name"`
	Email string `json:"email" gorm:"unique"`
}
```

- **Explanation**: Defines a `User` model with auto-managed fields by GORM (ID, CreatedAt, UpdatedAt, DeletedAt).

---

### **4. `controllers/user_controller.go`:**
```go
package controllers

import (
	"net/http"

	"user-crud-gin-gorm/models"

	"github.com/gin-gonic/gin"
	"gorm.io/gorm"
)

func GetUsers(c *gin.Context, db *gorm.DB) {
	var users []models.User
	db.Find(&users)
	c.JSON(http.StatusOK, users)
}

func GetUser(c *gin.Context, db *gorm.DB) {
	id := c.Param("id")
	var user models.User
	if err := db.First(&user, id).Error; err != nil {
		c.JSON(http.StatusNotFound, gin.H{"error": "User not found"})
		return
	}
	c.JSON(http.StatusOK, user)
}

func CreateUser(c *gin.Context, db *gorm.DB) {
	var user models.User
	if err := c.ShouldBindJSON(&user); err != nil {
		c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
		return
	}
	if err := db.Create(&user).Error; err != nil {
		c.JSON(http.StatusInternalServerError, gin.H{"error": err.Error()})
		return
	}
	c.JSON(http.StatusCreated, user)
}

func UpdateUser(c *gin.Context, db *gorm.DB) {
	id := c.Param("id")
	var user models.User
	if err := db.First(&user, id).Error; err != nil {
		c.JSON(http.StatusNotFound, gin.H{"error": "User not found"})
		return
	}
	if err := c.ShouldBindJSON(&user); err != nil {
		c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
		return
	}
	db.Save(&user)
	c.JSON(http.StatusOK, user)
}

func DeleteUser(c *gin.Context, db *gorm.DB) {
	id := c.Param("id")
	var user models.User
	if err := db.Delete(&user, id).Error; err != nil {
		c.JSON(http.StatusInternalServerError, gin.H{"error": err.Error()})
		return
	}
	c.JSON(http.StatusOK, gin.H{"message": "User deleted"})
}
```

- **Explanation**: Provides handler functions for each CRUD operation: get all users, get single user, create, update, and delete.

---

### **5. `routes/routes.go`:**
```go
package routes

import (
	"user-crud-gin-gorm/controllers"

	"github.com/gin-gonic/gin"
	"gorm.io/gorm"
)

func SetupRouter(db *gorm.DB) *gin.Engine {
	r := gin.Default()

	r.GET("/users", func(c *gin.Context) {
		controllers.GetUsers(c, db)
	})
	r.GET("/users/:id", func(c *gin.Context) {
		controllers.GetUser(c, db)
	})
	r.POST("/users", func(c *gin.Context) {
		controllers.CreateUser(c, db)
	})
	r.PUT("/users/:id", func(c *gin.Context) {
		controllers.UpdateUser(c, db)
	})
	r.DELETE("/users/:id", func(c *gin.Context) {
		controllers.DeleteUser(c, db)
	})

	return r
}
```

- **Explanation**: Routes requests to appropriate controller methods.

---

### **6. `main.go`:**
```go
package main

import (
	"log"
	"user-crud-gin-gorm/models"
	"user-crud-gin-gorm/routes"

	"gorm.io/driver/sqlite"
	"gorm.io/gorm"
)

func main() {
	db, err := gorm.Open(sqlite.Open("test.db"), &gorm.Config{})
	if err != nil {
		log.Fatal("Failed to connect database")
	}

	db.AutoMigrate(&models.User{})

	r := routes.SetupRouter(db)
	r.Run(":8080")
}
```

- **Explanation**: Initializes database, migrates the schema, and starts the Gin server.

---

### **7. Testing (`tests/user_controller_test.go`):**
```bash
go get github.com/stretchr/testify
```

Test code:
```go
package tests

import (
	"bytes"
	"net/http"
	"net/http/httptest"
	"testing"
	"user-crud-gin-gorm/models"
	"user-crud-gin-gorm/routes"

	"github.com/stretchr/testify/assert"
	"gorm.io/driver/sqlite"
	"gorm.io/gorm"
)

func setupTestDB() *gorm.DB {
	db, _ := gorm.Open(sqlite.Open(":memory:"), &gorm.Config{})
	db.AutoMigrate(&models.User{})
	return db
}

func TestCreateUser(t *testing.T) {
	db := setupTestDB()
	router := routes.SetupRouter(db)

	w := httptest.NewRecorder()
	reqBody := `{"name":"Alice","email":"alice@example.com"}`
	req, _ := http.NewRequest("POST", "/users", bytes.NewBufferString(reqBody))
	req.Header.Set("Content-Type", "application/json")

	router.ServeHTTP(w, req)

	assert.Equal(t, http.StatusCreated, w.Code)
	assert.Contains(t, w.Body.String(), "Alice")
}

func TestGetUsers(t *testing.T) {
	db := setupTestDB()
	db.Create(&models.User{Name: "Bob", Email: "bob@example.com"})
	router := routes.SetupRouter(db)

	w := httptest.NewRecorder()
	req, _ := http.NewRequest("GET", "/users", nil)
	router.ServeHTTP(w, req)

	assert.Equal(t, http.StatusOK, w.Code)
	assert.Contains(t, w.Body.String(), "Bob")
}

func TestUpdateUser(t *testing.T) {
	db := setupTestDB()
	user := models.User{Name: "Carol", Email: "carol@example.com"}
	db.Create(&user)

	router := routes.SetupRouter(db)

	w := httptest.NewRecorder()
	reqBody := `{"name":"Carol Updated","email":"carol.updated@example.com"}`
	req, _ := http.NewRequest("PUT", "/users/1", bytes.NewBufferString(reqBody))
	req.Header.Set("Content-Type", "application/json")
	router.ServeHTTP(w, req)

	assert.Equal(t, http.StatusOK, w.Code)
	assert.Contains(t, w.Body.String(), "Carol Updated")
}

func TestDeleteUser(t *testing.T) {
	db := setupTestDB()
	user := models.User{Name: "Dave", Email: "dave@example.com"}
	db.Create(&user)

	router := routes.SetupRouter(db)

	w := httptest.NewRecorder()
	req, _ := http.NewRequest("DELETE", "/users/1", nil)
	router.ServeHTTP(w, req)

	assert.Equal(t, http.StatusOK, w.Code)
	assert.Contains(t, w.Body.String(), "User deleted")
}
```

- **Explanation**: Tests each CRUD endpoint using an in-memory SQLite database. The tests verify correct HTTP responses and JSON content.

---

### Run Tests:
```bash
go test ./tests
```

### Run the Project:
```bash
go run main.go
```

Server runs at:  
`http://localhost:8080`

---

### **Conclusion:**
This complete, practical POC demonstrates:

- **CRUD API with Gin and GORM**
- **Clean project structure**
- **In-memory DB testing**
- **Clear, maintainable code and tests**
