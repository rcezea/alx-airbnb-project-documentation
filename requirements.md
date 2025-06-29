# Requirement Specifications for Backend Features

---

##  1. **User Authentication**

### **Functional Requirements**

* Users can register with email/password or via OAuth (Google/Facebook).
* Users receive JWT tokens after successful login.
* Roles are assigned as `guest`, `host`, or `admin`.

### **API Endpoints**

| Method | Endpoint             | Description                    |
| ------ | -------------------- | ------------------------------ |
| POST   | `/api/auth/register` | Register a new user            |
| POST   | `/api/auth/login`    | Login and retrieve JWT         |
| GET    | `/api/auth/profile`  | Get authenticated user profile |
| POST   | `/api/auth/oauth`    | OAuth login (Google/Facebook)  |

### **Input Specifications**

#### `/api/auth/register`

```json
{
  "email": "user@example.com",
  "password": "StrongPassword123",
  "role": "guest"
}
```

#### `/api/auth/login`

```json
{
  "email": "user@example.com",
  "password": "StrongPassword123"
}
```

### **Output**

```json
{
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "user": {
    "id": 1,
    "email": "user@example.com",
    "role": "guest"
  }
}
```

### **Validation Rules**

* Email must be unique and valid.
* Password must be 8+ characters, include one number and one uppercase.
* Role must be one of: `guest`, `host`, `admin`.

### **Performance Criteria**

* Auth endpoints must respond within 500ms.
* JWT token expiry: 1 hour; refresh token recommended for long sessions.

---

## 2. **Property Management**

### **Functional Requirements**

* Hosts can create, edit, and delete property listings.
* Listings contain details like title, price, amenities, and availability dates.

### **API Endpoints**

| Method | Endpoint              | Description          |
| ------ | --------------------- | -------------------- |
| POST   | `/api/properties`     | Create new listing   |
| GET    | `/api/properties`     | List all properties  |
| GET    | `/api/properties/:id` | Get property details |
| PUT    | `/api/properties/:id` | Update property      |
| DELETE | `/api/properties/:id` | Delete property      |

### **Input Specification (POST)**

```json
{
  "title": "Cozy 2-bedroom apartment",
  "description": "Close to downtown",
  "price_per_night": 150,
  "location": "Lagos, Nigeria",
  "amenities": ["wifi", "air conditioning", "parking"],
  "availability": {
    "start_date": "2025-07-01",
    "end_date": "2025-08-31"
  }
}
```

### **Output**

```json
{
  "id": 101,
  "host_id": 7,
  "title": "Cozy 2-bedroom apartment",
  ...
}
```

###  **Validation Rules**

* Title must be under 100 characters.
* Price must be a positive number.
* Dates must be valid and `end_date` > `start_date`.

###  **Performance Criteria**

* Max response time for GET /properties: 800ms with pagination.
* Limit to 10MB image upload per listing.

---

##  3. **Booking System**

###  **Functional Requirements**

* Guests can book available listings for specific dates.
* Prevent overlapping bookings.
* Allow cancellation by guest or host.

###  **API Endpoints**

| Method | Endpoint            | Description        |
| ------ | ------------------- | ------------------ |
| POST   | `/api/bookings`     | Create new booking |
| GET    | `/api/bookings`     | View user bookings |
| PUT    | `/api/bookings/:id` | Cancel a booking   |

### **Input Specification (POST)**

```json
{
  "property_id": 101,
  "start_date": "2025-08-01",
  "end_date": "2025-08-05"
}
```

### **Output**

```json
{
  "id": 45,
  "status": "pending",
  "total_price": 600,
  "booking_dates": {
    "start": "2025-08-01",
    "end": "2025-08-05"
  }
}
```

### **Validation Rules**

* Start date must be in the future.
* No overlapping bookings for the same property.
* Host cannot book their own property.

### **Performance Criteria**

* Booking check (availability + creation) in < 1000ms.
* Cancellation must update availability instantly.
