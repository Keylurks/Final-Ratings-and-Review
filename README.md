# Ratings and Reviews System

A production-ready ratings and reviews system for Philippine Komyut App, built with Spring Boot and PostgreSQL.

## Features

- ✅ Create, read, update, and delete reviews
- ✅ Star rating system (1-5 stars)
- ✅ Route-based review filtering
- ✅ Average rating calculation
- ✅ Review pagination and sorting
- ✅ Dark theme support
- ✅ Responsive design
- ✅ RESTful API

## Tech Stack

- **Backend**: Spring Boot 3.5.7, Java 17
- **Database**: PostgreSQL
- **Frontend**: Vanilla JavaScript, HTML5, CSS3
- **Security**: Spring Security
- **Monitoring**: Spring Boot Actuator

## Quick Start

### Prerequisites
- Java 17+
- Maven 3.6+
- PostgreSQL 12+

### Local Development

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd Ratings-and-Reviews-System-main
   ```

2. **Set up PostgreSQL database**
   ```bash
   createdb commuter_reviews
   ```

3. **Configure database connection**
   Update `src/main/resources/application.properties` or set environment variables:
   ```properties
   spring.datasource.url=jdbc:postgresql://localhost:5432/commuter_reviews
   spring.datasource.username=postgres
   spring.datasource.password=your_password
   ```

4. **Run database migration** (optional, for indexes and constraints)
   ```bash
   psql -d commuter_reviews -f src/main/resources/db/migration/V1__initial_schema.sql
   ```

5. **Run the application**
   ```bash
   ./mvnw spring-boot:run
   ```

6. **Access the application**
   - Frontend: http://localhost:8081
   - API: http://localhost:8081/api
   - Health Check: http://localhost:8081/actuator/health

## API Endpoints

### Reviews
- `POST /api/reviews` - Create a new review
- `GET /api/reviews/{id}` - Get review by ID
- `PUT /api/reviews/{id}?commuterId={commuterId}` - Update a review
- `DELETE /api/reviews/{id}?commuterId={commuterId}` - Delete a review

### Routes
- `GET /api/routes/{routeId}/reviews` - Get all reviews for a route
- `GET /api/routes/{routeId}/rating` - Get rating summary (average, count)

## Deployment to Render

See [RENDER_DEPLOYMENT.md](RENDER_DEPLOYMENT.md) for detailed deployment instructions.

### Quick Deploy Steps:
1. Connect your GitHub repository to Render
2. Create a PostgreSQL database in Render
3. Create a Web Service using the Dockerfile
4. Set environment variables (see RENDER_DEPLOYMENT.md)
5. Run database migration script
6. Deploy!

## Project Structure

```
src/
├── main/
│   ├── java/com/example/rrs/
│   │   ├── config/          # Configuration (Security, CORS)
│   │   ├── review/          # Review domain
│   │   │   ├── dto/         # Data Transfer Objects
│   │   │   ├── Review.java
│   │   │   ├── ReviewController.java
│   │   │   ├── ReviewService.java
│   │   │   └── ReviewRepository.java
│   │   └── common/          # Common utilities
│   └── resources/
│       ├── static/          # Frontend files
│       ├── db/migration/    # Database migrations
│       └── application.properties
└── test/                    # Test files
```

## Improvements Made

### Production Ready
- ✅ Health check endpoint for Render
- ✅ Production logging configuration
- ✅ CORS configuration
- ✅ Security headers
- ✅ Database indexes for performance
- ✅ Unique constraint to prevent duplicate reviews
- ✅ Improved XSS protection
- ✅ Environment-based configuration

### Bug Fixes
- ✅ Fixed theme toggle bug
- ✅ Fixed Dockerfile duplicate stages
- ✅ Improved error handling

## Future Enhancements

See [PRODUCTION_IMPROVEMENTS.md](PRODUCTION_IMPROVEMENTS.md) for a comprehensive list of recommended improvements including:
- Backend pagination
- Caching
- Rate limiting
- Authentication
- Review moderation
- And more...

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## License

This project is open source and available under the MIT License.

## Support

For issues and questions, please open an issue in the repository.
