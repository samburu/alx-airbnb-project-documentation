# Backend Requirements Documentation

This document outlines the technical and functional requirements for key backend features in the Airbnb Clone project.

---

## User Authentication

### Description
Handles user registration, login, session management, and authorization.

### API Endpoints

- `POST /api/v1/auth/register`
  - **Input**: `{ "first_name": "John", "last_name": "Doe", "email": "john@example.com", "password": "SecurePass123" }`
  - **Output**: `{ "message": "User registered successfully", "token": "<jwt_token>" }`
  - **Validation**:
    - Email must be valid format and unique
    - Password must be at least 8 characters
    - All fields are required

- `POST /api/v1/auth/login`
  - **Input**: `{ "email": "john@example.com", "password": "SecurePass123" }`
  - **Output**: `{ "message": "Login successful", "token": "<jwt_token>" }`
  - **Validation**:
    - Credentials must match existing record

- `GET /api/v1/auth/profile`
  - **Header**: Authorization: Bearer `<jwt_token>`
  - **Output**: User profile information
  - **Validation**: JWT must be valid and not expired

### Performance Criteria
- Response time < 300ms under 1,000 concurrent users
- Secure password storage using bcrypt
- JWT expiration: 1 hour

---

## Property Management

### Description
Allows hosts to add, edit, delete, and view their property listings.

### API Endpoints

- `POST /api/v1/properties`
  - **Input**: `{ "name": "Modern Loft", "description": "2-bed loft", "location": "Nairobi", "pricepernight": 50.00 }`
  - **Output**: `{ "message": "Property added", "property_id": "<uuid>" }`
  - **Validation**:
    - Name, location, and description required
    - Price must be a positive decimal

- `PUT /api/v1/properties/:id`
  - **Input**: Any updated fields
  - **Output**: `{ "message": "Property updated" }`
  - **Authorization**: Host must own the listing

- `DELETE /api/v1/properties/:id`
  - **Output**: `{ "message": "Property deleted" }`

- `GET /api/v1/properties`
  - **Output**: List of all properties
  - **Filtering**: By location, price range, amenities (query parameters)

### Performance Criteria
- Search results return in < 500ms with pagination
- Rate limit of 10 property creations per user per day

---

## Booking System

### Description
Allows guests to create and manage bookings for properties.

### API Endpoints

- `POST /api/v1/bookings`
  - **Input**: `{ "property_id": "<uuid>", "start_date": "2025-07-01", "end_date": "2025-07-05" }`
  - **Output**: `{ "message": "Booking created", "booking_id": "<uuid>" }`
  - **Validation**:
    - Dates must be valid and not overlap with existing bookings
    - User cannot book own property

- `GET /api/v1/bookings/:id`
  - **Output**: Booking details
  - **Authorization**: Must be the guest who made the booking or the host of the property

- `DELETE /api/v1/bookings/:id`
  - **Output**: `{ "message": "Booking canceled" }`
  - **Policy**: Must cancel before start_date

### Performance Criteria
- Prevent race conditions using transactions
- Booking conflict resolution within 100ms

---
