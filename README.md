## Golang GraphQL API

This is a simple GraphQL API using Golang and Sqlite3.

## Steps to initialize the project
### 1. Create tools.go file
```go
//go:build tools

package tools

import (
	_ "github.com/99designs/gqlgen"
)
```

### 2. Install gqlgen dependencies
```bash
$ go mod tidy
```

### 3. Create GraphQL schemas
>Path of schemas `./graph/schema.graphqls`.

### 4. Generate GraphQL code
```bash
$ go run github.com/99designs/gqlgen generate
```

### 5. Separate models in different files
>Path of models `./graph/model`.
> - category.go # Remove the `Courses` field (Course    *Course `json:"course"`).
> - course.go   # Remove the `Caterories` field (Category    *Category `json:"category"`).

> Change `./gqlgen.yml` to point to the new models.
> ```yaml
> ...
> models:
>   Category:
>     model:
>       - github.com/tiagoncardoso/golang-graphql/graph/model.Category
>   Course:
>     model:
>       - github.com/tiagoncardoso/golang-graphql/graph/model.Course
> ...

### 6. Regenerate GraphQL code
```bash
$ go run github.com/99designs/gqlgen generate
```

### 7. Create the database tables structure
```bash
$ sqlite3 data.db
$ create table categories (id string, name string, description string);
$ create table courses (id string, name string, description string, category_id string);
```

---
## Mutations

### Create a new category

```graphql
mutation createCategory {
  createCategory(input:{name:"Marketing", description:"cursos de marketing"}){
    id
    name
    description
  }
}
```

### Create a new course

```graphql
mutation createCourse {
  createCourse(input:{name:"Marketing Pessoal", description:"Curso marketing pessoal", categoryId:"acaef670-bd71-4290-8546-85eceb540ac7"}){
    id
    name
    description
  }
}
```

## Queries

### Get Categories

```graphql
query findAllCategoriesWithoutNestedCourses {
  categories{
    id
    name
    description
  }
}
```

```graphql
query findCategoriesWithCourses{
  categories{
    id
    name
    description
    courses{
      id
      name
    }
  }
}
```

### Get Courses

```graphql
query findAllCoursesWithoutCategoryDetails{
  courses{
    id
    name
  }
}
```

```graphql
query findCoursesWithCategory {
  courses{
    id
    name
    description
    category{
      id
      name
    }
  }
}
```