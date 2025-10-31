# GoBid - Real-time Auction Platform

A robust real-time auction platform built with Go, featuring WebSocket support for live bidding, secure authentication, and PostgreSQL database integration. Developed as part of the Rocketseat Go course.

## Features

- **User Authentication**: Secure user registration and login system with session management
- **Real-time Bidding**: WebSocket-based live auction rooms for real-time bid updates
- **Product Management**: Create and manage auction products with base prices and time limits
- **Secure Sessions**: PostgreSQL-backed session storage with CSRF protection
- **RESTful API**: Clean API design following REST principles
- **Docker Support**: Full containerization with Docker and Docker Compose

## Tech Stack

- **Language**: Go 1.23.4
- **Web Framework**: [Chi Router](https://github.com/go-chi/chi) v5.2.0
- **Database**: PostgreSQL with [pgx](https://github.com/jackc/pgx) driver
- **Session Management**: [SCS](https://github.com/alexedwards/scs) with PostgreSQL store
- **WebSocket**: Gorilla WebSocket for real-time communication
- **Authentication**: bcrypt for password hashing
- **SQL Generation**: sqlc for type-safe SQL
- **Migration Tool**: Tern for database migrations
- **Containerization**: Docker & Docker Compose

## Project Structure

```
gobid/
├── cmd/
│   ├── api/              # Main application entry point
│   └── terndotenv/        # Migration tool with env support
├── internal/
│   ├── api/               # HTTP handlers and routes
│   │   ├── routes.go      # API route definitions
│   │   ├── auth.go        # Authentication middleware
│   │   ├── user_handlers.go
│   │   ├── product_handlers.go
│   │   └── auction_handler.go
│   ├── services/          # Business logic layer
│   │   ├── users_service.go
│   │   ├── products_service.go
│   │   ├── bids_service.go
│   │   └── auction_services.go
│   ├── usecase/           # Use case implementations
│   │   ├── user/
│   │   └── product/
│   ├── store/             # Database layer
│   │   └── pgstore/
│   │       ├── migrations/  # SQL migrations
│   │       ├── queries/     # SQL queries
│   │       └── *.sql.go     # Generated SQL code
│   ├── jsonutils/         # JSON utility functions
│   └── validator/         # Input validation
├── docker-compose.yml
├── Dockerfile
├── Dockerfile.prod
└── go.mod
```

## API Endpoints

### Health Check

- `GET /health` - Application health status

### Authentication

- `POST /api/v1/users/signup` - Register a new user
- `POST /api/v1/users/login` - User login
- `POST /api/v1/users/logout` - User logout (requires auth)

### Products & Auctions

- `POST /api/v1/products/` - Create a new auction product (requires auth)
- `GET /api/v1/products/ws/subscribe/{product_id}` - WebSocket endpoint for real-time auction updates (requires auth)

## Getting Started

### Prerequisites

- Go 1.23.4 or higher
- Docker and Docker Compose
- PostgreSQL (if running without Docker)

### Environment Setup

1. Clone the repository:

```bash
git clone https://github.com/rocketseat-education/gobid.git
cd gobid
```

2. Copy the environment variables:

```bash
cp .env.example .env
```

3. Update the `.env` file with your configuration:

```env
GOBID_APP_PORT=3000
GOBID_DATABASE_PORT=5432
GOBID_DATABASE_NAME=gobid
GOBID_DATABASE_USER=postgres
GOBID_DATABASE_PASSWORD=your_secure_password
GOBID_DATABASE_HOST=localhost
GOBID_CSRF_KEY=your_32_character_csrf_key
```

### Running with Docker Compose (Recommended)

```bash
# Start all services
docker-compose up

# Run in background
docker-compose up -d

# Stop services
docker-compose down
```

The application will be available at `http://localhost:3000` (or the port specified in GOBID_APP_PORT).

### Running Locally

1. Install dependencies:

```bash
go mod download
```

2. Set up the database:

```bash
# Create the database
createdb gobid

# Run migrations (you'll need to set up Tern)
go run cmd/terndotenv/main.go migrate -c internal/store/pgstore/migrations/tern.conf
```

3. Run the application:

```bash
go run cmd/api/main.go
```

## Database Migrations

The project uses Tern for database migrations. Migration files are located in `internal/store/pgstore/migrations/`.

To run migrations:

```bash
go run cmd/terndotenv/main.go migrate -c internal/store/pgstore/migrations/tern.conf
```

## Development

### Generating SQL Code

The project uses sqlc to generate type-safe Go code from SQL queries:

```bash
# Install sqlc
go install github.com/kyleconroy/sqlc/cmd/sqlc@latest

# Generate code
cd internal/store/pgstore
sqlc generate
```

### Running Tests

```bash
go test ./...
```

### Building for Production

Using Docker:

```bash
docker build -f Dockerfile.prod -t gobid:latest .
```

Using Go:

```bash
go build -o bin/gobid cmd/api/main.go
```

## WebSocket Communication

The auction system uses WebSocket for real-time bid updates. Connect to `/api/v1/products/ws/subscribe/{product_id}` to receive live updates for a specific auction.

### Message Format

Bid messages are exchanged in JSON format containing:

- User information
- Bid amount
- Timestamp
- Product details

## Security Features

- **Password Hashing**: bcrypt for secure password storage
- **Session Management**: Secure cookie-based sessions with PostgreSQL storage
- **CSRF Protection**: Built-in CSRF token validation (configurable)
- **Input Validation**: Request validation for all endpoints
- **SQL Injection Prevention**: Parameterized queries via sqlc

## Deployment

The project includes a GitHub Actions workflow example for automated deployment. See `.github.example/workflows/deploy.yml` for the CI/CD pipeline configuration.

## License

This project is part of the Rocketseat education platform and is intended for educational purposes.

## Acknowledgments

- [Rocketseat](https://www.rocketseat.com.br/) for the excellent Go course
- The Go community for amazing libraries and tools

## Support

For issues, questions, or suggestions, please open an issue in the GitHub repository.
