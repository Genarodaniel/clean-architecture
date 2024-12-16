# Clean Architecture

This is a project to study Clean Architecture and implement it in a simple order service.

## Architectural Boundaries

- **Separate Details:** Tools like databases, queue systems, and frontend technologies should exist outside the core business rules.
- **Entities:** Represent the business rules.

## Key Concepts About Architecture

- **Structure and Components:** Think about the structure of the software and the division of its components. The architecture defines how components communicate, much like an elevator or staircase in a building connects floors.
- **Facilitation:** A good architecture facilitates development, deployment, operation, and maintenance of the application.
- **Flexibility:** "The strategy behind that facilitation is to leave as many options open as possible, for as long as possible."

## Keep Options Open

- **Business Rules First:** The core value of the software lies in its business rules.
- **Details Are Secondary:** Technologies like RabbitMQ, SQS, or specific databases are details; the focus should be on managing queues or data, regardless of the tool used.
- **Independence:** Frameworks, databases, or APIs should not impact business rules.

## Use Cases

- **Intentions:** Use cases represent the intentions of the software's components. Each action corresponds to a specific use case.
- **Clarify Behavior:** Define the expected behavior of the software.
- **Details Postponed:** Delay decisions on specifics like the database or queue system as much as possible.

### Use Cases Tell a Story

- A use case defines the sequence of actions in automation:
  1. Receive parameters.
  2. Validate data (e.g., name, address).
  3. Perform calculations or checks (e.g., credit score).
  4. Make decisions based on validations.
- Business rules reside within entities.

### Single Responsibility Principle (SRP)

- Avoid reusing use cases just because they seem similar. For example, "Update" and "Insert" may share some functionality but serve distinct purposes and should remain separate.
- Each use case should change for a single reason.

### Repetition

- **Accept Real Repetition:** Some code repetition is acceptable if it reflects different intentions, such as validating a record before insertion or updating.

## Input and Output

- Simplify your logic by focusing on input and output.
  - Example: For an order service, `Order Data` is the input, and `Order Created` is the output.

### Important Workflow

1. External interfaces (e.g., GraphQL, REST, gRPC, CLI) receive input data.
2. Controllers validate the input and send it to the use cases.
3. Use cases perform the intended operations using business rules.
4. Use cases return outputs to the controllers, which format the response for the external interface.

### Example Workflow

- Controller -> Use Case Input Port -> Use Case Interactor -> Presenter -> Use Case Output Port -> Controller -> Response
- **Presenter:** Formats the output to match the required response format (e.g., JSON, XML).

### Folder Organization in Go

A possible folder structure for a project following Clean Architecture principles in Go:

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
  - **entity/**: Holds business entities and domain models (e.g., Order, Customer).
  - **usecase/**: Contains the application’s use cases, implementing the core business workflows.
  - **interface/**: Contains adapters for interacting with external systems, split into controllers (input) and presenters (output).
  - **repository/**: Abstracts interactions with data sources, like databases or external APIs.
- **pkg/**: Optional. Houses shared libraries that can be reused across projects.
- **configs/**: Contains configuration files like YAML or JSON for managing environment-specific settings.
- **docs/**: Stores project documentation, such as API specifications or architecture overviews.

This structure enforces separation of concerns, making the application easier to maintain and scale.

## Presenters

- Transform output DTOs into the required format for delivery.

### Complete Implementation Example in Go

```go
package main

import (
	"encoding/json"
	"encoding/xml"
	"fmt"
)

type CategoryInputDTO struct {
	Name string `json:"name"`
}

type CategoryOutputDTO struct {
	ID   int    `json:"id"`
	Name string `json:"name"`
}

func CreateCategoryUseCase(input CategoryInputDTO) CategoryOutputDTO {
	// Simulate creating a category and returning the result
	return CategoryOutputDTO{
		ID:   1,
		Name: input.Name,
	}
}

type CategoryPresenter struct {
	Output CategoryOutputDTO
}

func (p CategoryPresenter) ToJson() string {
	result, _ := json.Marshal(p.Output)
	return string(result)
}

func (p CategoryPresenter) ToXml() string {
	result, _ := xml.MarshalIndent(p.Output, "", "  ")
	return string(result)
}

func main() {
	input := CategoryInputDTO{Name: "Electronics"}
	output := CreateCategoryUseCase(input)
	presenter := CategoryPresenter{Output: output}

	jsonResult := presenter.ToJson()
	xmlResult := presenter.ToXml()

	fmt.Println("JSON Result:")
	fmt.Println(jsonResult)
	fmt.Println("\nXML Result:")
	fmt.Println(xmlResult)
}
```

## Entities

- **In Clean Architecture:** Entities represent the business rules layer.
- **In DDD:** Entities represent unique objects within the application.
- **Combination:** Entities in Clean Architecture typically combine aggregates and domain services.
- They are independent of specific situations and reusable across contexts.
