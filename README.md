# Clean Architecture

This is a project to study Clean Architecture and implement it in a simple category service.

## Entities

### What Are Entities in Clean Architecture?
Entities represent the core business rules and are independent of specific applications or frameworks. They encapsulate the critical data and behavior central to the domain and remain unchanged across various contexts.

### Characteristics of Entities
- **Core Business Logic:** Entities are where the most critical business rules are implemented.
- **Independence:** Entities do not rely on external frameworks, databases, or tools. They are designed to be framework-agnostic.
- **Reusability:** Entities can be reused across different applications or layers without modification.

### Example: Category Entity in Go
Here is an example of how an entity for a "Category" might look in Go:

```go
package entity

import (
	"errors"
)

type Category struct {
	ID   int
	Name string
}

func NewCategory(name string) (*Category, error) {
	if name == "" {
		return nil, errors.New("category name cannot be empty")
	}
	return &Category{
		ID:   0, // ID will be set when persisted
		Name: name,
	}, nil
}

func (c *Category) Rename(newName string) error {
	if newName == "" {
		return errors.New("new category name cannot be empty")
	}
	c.Name = newName
	return nil
}
```

### Explanation
- The `Category` struct encapsulates the business rules for managing categories.
- Validation rules, such as checking for an empty name, are built into the entity's methods.
- Entities like `Category` are used by the application use cases to implement business workflows.

## Use Cases

### What Are Use Cases in Clean Architecture?
Use cases define the specific application behaviors, representing the intentions of the system. They coordinate the flow of data between external interfaces and the business entities to fulfill user or system actions.

### Characteristics of Use Cases
- **Focus on Behavior:** Use cases describe *how* the application interacts with entities to accomplish a task.
- **Independent of Details:** They do not concern themselves with specific technologies or frameworks.
- **Encapsulation:** Each use case is responsible for a single action, such as creating or updating a category.

### Example: Create Category Use Case in Go
Here’s an example of implementing a use case to create a category:

```go
package usecase

import (
	"errors"
	"myproject/internal/entity"
)

type CreateCategoryInput struct {
	Name string
}

type CreateCategoryOutput struct {
	ID   int
	Name string
}

type CategoryRepository interface {
	Save(category *entity.Category) (*entity.Category, error)
}

type CreateCategoryUseCase struct {
	repo CategoryRepository
}

func NewCreateCategoryUseCase(repo CategoryRepository) *CreateCategoryUseCase {
	return &CreateCategoryUseCase{repo: repo}
}

func (uc *CreateCategoryUseCase) Execute(input CreateCategoryInput) (*CreateCategoryOutput, error) {
	if input.Name == "" {
		return nil, errors.New("category name cannot be empty")
	}

	category, err := entity.NewCategory(input.Name)
	if err != nil {
		return nil, err
	}

	savedCategory, err := uc.repo.Save(category)
	if err != nil {
		return nil, err
	}

	return &CreateCategoryOutput{
		ID:   savedCategory.ID,
		Name: savedCategory.Name,
	}, nil
}
```

### Explanation
1. **Input and Output DTOs:**
   - `CreateCategoryInput` captures the input required for the use case.
   - `CreateCategoryOutput` represents the response returned after execution.
2. **Dependency on a Repository:**
   - The use case relies on a `CategoryRepository` interface for persisting the entity. This ensures the use case is not tied to a specific database implementation.
3. **Workflow:**
   - Validate the input.
   - Create a `Category` entity using its business rules.
   - Save the entity using the repository.
   - Return the saved entity's details.

## Repository

### What Is a Repository in Clean Architecture?
A repository provides an abstraction layer between the domain entities and the data storage mechanism. It allows use cases to interact with data sources without needing to know the specifics of database operations.

### Characteristics of a Repository
- **Abstraction:** The repository defines a contract (interface) for data operations, hiding the details of the underlying storage.
- **Flexibility:** By adhering to the interface, the implementation can be swapped out (e.g., from one database to another) without affecting the business logic.
- **Testability:** Use cases can be tested with mock implementations of the repository.

### Example: Repository Implementation
Here’s a simple implementation of the `CategoryRepository` interface:

```go
package repository

import (
	"errors"
	"myproject/internal/entity"
)

type CategoryRepositoryImpl struct {
	categories []entity.Category
	nextID     int
}

func NewCategoryRepository() *CategoryRepositoryImpl {
	return &CategoryRepositoryImpl{
		categories: []entity.Category{},
		nextID:     1,
	}
}

func (r *CategoryRepositoryImpl) Save(category *entity.Category) (*entity.Category, error) {
	if category == nil {
		return nil, errors.New("category is nil")
	}

	category.ID = r.nextID
	r.nextID++
	r.categories = append(r.categories, *category)

	return category, nil
}
```

### Workflow Example
Here’s an example of how to wire up everything and execute the use case:

```go
package main

import (
	"fmt"
	"myproject/internal/entity"
	"myproject/internal/repository"
	"myproject/internal/usecase"
)

func main() {
	repo := repository.NewCategoryRepository()
	useCase := usecase.NewCreateCategoryUseCase(repo)

	input := usecase.CreateCategoryInput{Name: "Electronics"}
	output, err := useCase.Execute(input)
	if err != nil {
		fmt.Println("Error:", err)
		return
	}

	fmt.Printf("Category Created: ID=%d, Name=%s\n", output.ID, output.Name)
}
```

### Key Points
- **Abstraction in Action:** The `CategoryRepository` interface defines the data persistence contract, while the implementation fulfills it.
- **Separation of Concerns:** The repository ensures that the persistence logic is separate from the business logic.
- **Flexibility for Growth:** Additional implementations (e.g., SQL, NoSQL, or external APIs) can be added later by adhering to the same interface.

## Folder Organization in Clean Architecture
Here’s an example of how to organize folders for a project following Clean Architecture principles:

```plaintext
project/
│
├── cmd/                # Main applications of the project
│   ├── api/            # HTTP or gRPC server initialization
│   └── cli/            # Command-line tool initialization
│
├── internal/           # Application-specific code
│   ├── entity/         # Business entities and domain logic
│   ├── usecase/        # Application use cases
│   ├── interface/      # Interfaces for external systems
│   │   ├── controller/ # HTTP or gRPC controllers
│   │   └── presenter/  # Format output for various interfaces (JSON, XML, etc.)
│   └── repository/     # Abstraction for data storage and retrieval
│
├── pkg/                # Shared utility packages (optional)
│
├── configs/            # Configuration files
│
└── docs/               # Documentation
```

### Explanation of Folders
- **cmd/**: Contains the entry points for your application. Separate subfolders can manage HTTP servers, gRPC services, or CLI tools.
- **internal/**: The core application logic, further divided into:
  - **entity/**: Holds business entities and domain models (e.g., `Category`).
  - **usecase/**: Contains the application’s use cases, implementing the core business workflows.
  - **interface/**: Contains adapters for interacting with external systems, split into controllers (input) and presenters (output).
  - **repository/**: Abstracts interactions with data sources, like databases or external APIs.
- **pkg/**: Optional. Houses shared libraries that can be reused across projects.
- **configs/**: Contains configuration files like YAML or JSON for managing environment-specific settings.
- **docs/**: Stores project documentation, such as API specifications or architecture overviews.

This structure

