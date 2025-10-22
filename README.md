---

# FastAPI TODO Application

This repository contains the backend codebase for a **FastAPI TODO Application**, designed to provide a robust API for managing user accounts and their associated to-do lists. The application leverages **FastAPI** for building the API, **SQLAlchemy** for Object Relational Mapping (ORM), and **PostgreSQL** as the database.

## Table of Contents

*   [Features]
*   [Technologies Used]
*   [Project Structure]
*   [Setup and Installation]
*   [Database Migrations]
*   [Running the Application]
*   [API Endpoints]

## Features

The application provides comprehensive functionalities for user and to-do management, including:

*   **User Authentication and Authorization**:
    *   **User Registration**: Create new user accounts with unique usernames and emails.
    *   **User Login**: Authenticate users and issue JSON Web Tokens (JWT) for secure access.
    *   **Password Hashing**: Securely store user passwords using `bcrypt` context.
    *   **Password Change**: Allows authenticated users to change their password.
    *   **Phone Number Update**: Allows authenticated users to update their phone number.
    *   **Current User Retrieval**: Fetch details of the currently authenticated user.
*   **To-Do Management (User-specific)**:
    *   **Create To-Do**: Add new to-do items with title, description, priority, and completion status.
    *   **Read All To-Dos**: Retrieve all to-do items belonging to the authenticated user.
    *   **Read Specific To-Do**: Fetch a single to-do item by its ID, ensuring it belongs to the user.
    *   **Update To-Do**: Modify existing to-do items by ID, restricted to the owner.
    *   **Delete To-Do**: Remove a to-do item by ID, restricted to the owner.
*   **Admin Functionalities**:
    *   **Read All To-Dos**: Administrators can view all to-do items across all users.
    *   **Delete Any To-Do**: Administrators can delete any to-do item by ID.

## Technologies Used

*   **FastAPI**: A modern, fast (high-performance) web framework for building APIs with Python 3.7+ based on standard Python type hints.
*   **SQLAlchemy**: Python SQL toolkit and Object Relational Mapper that gives application developers the full power and flexibility of SQL.
*   **PostgreSQL**: A powerful, open-source object-relational database system.
*   **`passlib`**: For hashing passwords using bcrypt.
*   **`python-jose`**: For handling JSON Web Tokens (JWT) for authentication.
*   **Alembic**: A lightweight database migration tool for SQLAlchemy.

## Project Structure

The codebase is organized into several Python files, each serving a specific purpose:

*   **`database.py`**:
    *   Configures the **SQLAlchemy engine** to connect to your PostgreSQL database. The database URL for PostgreSQL is defined as `SQLALCHEMY_DATABASE_URL`.
    *   Sets up `SessionLocal` for database session management and `Base` for declarative model definition.
*   **`models.py`**:
    *   Defines the SQLAlchemy database models: `Users` and `Todos`.
    *   The `Users` table includes fields for `id`, `email`, `username`, `first_name`, `last_name`, `hashed_password`, `is_active`, `role`, and `phone_number`.
    *   The `Todos` table includes `id`, `title`, `description`, `priority`, `complete`, and `owner_id` (a **Foreign Key** linking to `users.id`).
*   **`main.py`**:
    *   The **main FastAPI application instance** (`app = FastAPI()`).
    *   Initializes database tables by calling `models.Base.metadata.create_all(bind=engine)`.
    *   **Includes routers** from `auth`, `todos`, `admin`, and `users` modules to organize API endpoints.
*   **`auth.py`**:
    *   Manages **user authentication and registration**.
    *   Defines `SECRET_KEY` and `ALGORITHM` for JWT handling.
    *   Includes `bcrypt_context` for password hashing.
    *   Provides API endpoints for `POST /auth/` (user creation) and `POST /auth/token` (login to get access token).
    *   Contains helper functions like `authenticate_user`, `create_access_token`, and `get_current_user` for authentication flow.
*   **`admin.py`**:
    *   Contains API endpoints specifically for **administrative tasks**, such as `GET /admin/todo` to retrieve all to-do items and `DELETE /admin/todo/{todo_id}` to delete any to-do.
    *   These endpoints require the authenticated user to have an `Admin` role.
*   **`users.py`**:
    *   Handles **user-specific profile management**.
    *   Endpoints include `GET /user/` to retrieve the current user's details, `PUT /user/password` to change password, and `PUT /user/phonenumber/{phone_number}` to update the phone number.
    *   Uses `bcrypt_context` for password verification and hashing during password changes.
*   **`todo.py`**:
    *   Manages **user-specific to-do operations**.
    *   Provides CRUD (Create, Read, Update, Delete) operations for to-do items for the authenticated user's `owner_id`.
    *   Includes endpoints like `GET /` (user's todos), `GET /todo/{todo_id}`, `POST /todo`, `PUT /todo/{todo_id}`, and `DELETE /todo/{todo_id}`.
*   **`alembic.ini`**:
    *   The primary **configuration file for Alembic**, specifying the script location, database URL (`sqlalchemy.url = postgresql://postgres:Admin%%401234@localhost/TodoApplicationDatabase`), and logging settings.
*   **`alembic/versions/6c94f28e85fa_create_column_phone_number_on_users_.py`**:
    *   An **example Alembic migration script** that adds a `phone_number` column to the `users` table in the `upgrade` function and drops it in the `downgrade` function. This demonstrates how database schema changes are managed.

## Setup and Installation

To set up and run the application locally, follow these steps:

1.  **Clone the repository**:
    ```bash
    git clone https://github.com/vmishra710/Todo_Application_FastAPI.git
    cd Todo_Application_FastAPI
    ```

2.  **Create a Virtual Environment** (Highly Recommended):
    ```bash
    python -m venv venv
    source venv/bin/activate  # On Windows, use `venv\Scripts\activate`
    ```

3.  **Install Dependencies**:
    Install all required Python packages using pip:
    ```bash
    pip install fastapi "uvicorn[standard]" sqlalchemy "psycopg2-binary" "passlib[bcrypt]" "python-jose[cryptography]" pydantic alembic
    ```

4.  **Database Configuration**:
    *   Ensure you have a **PostgreSQL** database instance running.
    *   The application is configured to connect to `postgresql://postgres:Admin%401234@localhost/TodoApplicationDatabase`.
    *   **You may need to update this URL** in `database.py` and `alembic.ini` if your database credentials or host differ.

## Database Migrations

This project uses **Alembic** for managing database schema changes.

1.  **Initialize Alembic** (if not already done, usually done once per project):
    ```bash
    alembic init alembic
    ```
    *Note: The sources indicate the `alembic` directory and `alembic.ini` are already part of the codebase.*

2.  **Run Migrations**:
    To apply all pending migrations and create the necessary tables (Users, Todos) and columns (e.g., `phone_number`), execute:
    ```bash
    alembic upgrade head
    ```
    This will execute the `upgrade` function for all migration scripts, such as the one found in `alembic/versions/6c94f28e85fa_create_column_phone_number_on_users_.py`.

## Running the Application

After setting up the database and installing dependencies, you can run the FastAPI application using Uvicorn:

```bash
uvicorn main:app --reload
```
This command will start the development server, and the API will be accessible at `http://127.0.0.1:8000` (default port). The `--reload` flag enables auto-reloading upon code changes.

## API Endpoints

Here's a summary of the main API endpoints provided by the application:

### Authentication Endpoints (`/auth`)

*   **`POST /auth/`**
    *   **Purpose**: Create a new user account.
    *   **Request Body**: `CreateUserRequest` containing `username`, `email`, `firstname`, `lastname`, `password`, `role`, `phone_number`.
    *   **Response**: Status 201 CREATED.
*   **`POST /auth/token`**
    *   **Purpose**: Authenticate a user and receive an access token.
    *   **Request Body**: `OAuth2PasswordRequestForm` (username, password).
    *   **Response**: `Token` model with `access_token` and `token_type`.

### User Profile Endpoints (`/user`)

*   **`GET /user/`**
    *   **Purpose**: Get details of the current authenticated user.
    *   **Authentication**: Requires valid JWT.
    *   **Response**: User details.
*   **`PUT /user/password`**
    *   **Purpose**: Change the authenticated user's password.
    *   **Authentication**: Requires valid JWT.
    *   **Request Body**: `UserVerification` model with `password` (current) and `new_password`.
    *   **Response**: Status 204 NO CONTENT.
*   **`PUT /user/phonenumber/{phone_number}`**
    *   **Purpose**: Update the authenticated user's phone number.
    *   **Authentication**: Requires valid JWT.
    *   **Path Parameter**: `phone_number`.
    *   **Response**: Status 204 NO CONTENT.

### To-Do Endpoints (User-Specific - `/`)

*   **`GET /`**
    *   **Purpose**: Retrieve all to-do items for the authenticated user.
    *   **Authentication**: Requires valid JWT.
    *   **Response**: List of `Todos` objects.
*   **`GET /todo/{todo_id}`**
    *   **Purpose**: Retrieve a specific to-do item by ID for the authenticated user.
    *   **Authentication**: Requires valid JWT.
    *   **Path Parameter**: `todo_id` (must be greater than 0).
    *   **Response**: Specific `Todos` object or 404 if not found/owned.
*   **`POST /todo`**
    *   **Purpose**: Create a new to-do item for the authenticated user.
    *   **Authentication**: Requires valid JWT.
    *   **Request Body**: `TodoRequest` containing `title`, `description`, `priority`, `complete`.
    *   **Response**: Status 201 CREATED.
*   **`PUT /todo/{todo_id}`**
    *   **Purpose**: Update an existing to-do item by ID for the authenticated user.
    *   **Authentication**: Requires valid JWT.
    *   **Path Parameter**: `todo_id` (must be greater than 0).
    *   **Request Body**: `TodoRequest` for updated details.
    *   **Response**: Status 204 NO CONTENT or 404 if not found/owned.
*   **`DELETE /todo/{todo_id}`**
    *   **Purpose**: Delete a to-do item by ID for the authenticated user.
    *   **Authentication**: Requires valid JWT.
    *   **Path Parameter**: `todo_id`.
    *   **Response**: Status 204 NO CONTENT or 404 if not found/owned.

### Admin Endpoints (`/admin`)

*   **`GET /admin/todo`**
    *   **Purpose**: Retrieve all to-do items from the database (for administrators).
    *   **Authentication**: Requires valid JWT with `user_role` as 'Admin'.
    *   **Response**: List of all `Todos` objects.
*   **`DELETE /admin/todo/{todo_id}`**
    *   **Purpose**: Delete any to-do item by ID (for administrators).
    *   **Authentication**: Requires valid JWT with `user_role` as 'Admin'.
    *   **Path Parameter**: `todo_id` (must be greater than 0).
    *   **Response**: Status 204 NO CONTENT or 401/404 if not found/unauthorized.
