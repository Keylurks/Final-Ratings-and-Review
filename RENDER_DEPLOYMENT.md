# Render Deployment Guide

## Environment Variables Required

Set these in your Render dashboard:

### Database Connection
- `SPRING_DATASOURCE_URL` - PostgreSQL connection URL (provided by Render)
- `SPRING_DATASOURCE_USERNAME` - Database username (provided by Render)
- `SPRING_DATASOURCE_PASSWORD` - Database password (provided by Render)

### Application Configuration
- `PORT` - Server port (usually set automatically by Render)
- `SPRING_PROFILES_ACTIVE=prod` - Use production profile (optional, defaults to prod in Dockerfile)

### Optional Configuration
- `JPA_DDL_AUTO=validate` - Database schema validation (recommended for production)
- `JPA_SHOW_SQL=false` - Disable SQL logging in production
- `LOG_SQL=WARN` - Set SQL logging level
- `LOG_HIKARI=WARN` - Set connection pool logging level

## Database Setup

### Initial Schema
The application uses `ddl-auto=update` by default for development. For production:

1. **Option 1: Use Flyway/Liquibase** (Recommended)
   - Add Flyway dependency to `pom.xml`
   - Migration scripts are in `src/main/resources/db/migration/`
   - Run the migration script manually first time

2. **Option 2: Manual Setup**
   - Run the SQL script in `src/main/resources/db/migration/V1__initial_schema.sql` on your database
   - This creates indexes and unique constraints

### Required Database Changes
Run this SQL on your PostgreSQL database:

```sql
-- Add unique constraint to prevent duplicate reviews
ALTER TABLE reviews 
ADD CONSTRAINT unique_route_commuter 
UNIQUE (route_id, commuter_id);

-- Add indexes for performance
CREATE INDEX IF NOT EXISTS idx_reviews_route_id ON reviews(route_id);
CREATE INDEX IF NOT EXISTS idx_reviews_commuter_id ON reviews(commuter_id);
CREATE INDEX IF NOT EXISTS idx_reviews_created_at ON reviews(created_at DESC);
CREATE INDEX IF NOT EXISTS idx_reviews_route_created ON reviews(route_id, created_at DESC);
```

## Health Check

The application exposes a health check endpoint at:
- `/actuator/health`

Configure Render to use this endpoint for health checks.

## Build Configuration

The Dockerfile is configured for Render:
- Uses Java 17
- Exposes port 8081
- Sets production profile by default
- Optimized JVM settings for Render's environment

## Deployment Steps

1. **Connect Repository**: Link your GitHub repository to Render
2. **Create PostgreSQL Database**: Create a new PostgreSQL database in Render
3. **Create Web Service**: 
   - Use the Dockerfile for deployment
   - Set environment variables (see above)
   - Set health check path: `/actuator/health`
4. **Run Database Migration**: Execute the SQL script manually or use Flyway
5. **Deploy**: Render will build and deploy automatically

## Post-Deployment

1. Verify health check: `https://your-app.onrender.com/actuator/health`
2. Test API endpoints: `https://your-app.onrender.com/api/routes/1/reviews`
3. Check logs in Render dashboard for any errors

## Troubleshooting

### Database Connection Issues
- Verify environment variables are set correctly
- Check database is running and accessible
- Verify connection string format

### Health Check Failing
- Check application logs
- Verify database connection
- Ensure port 8081 is exposed

### Performance Issues
- Verify database indexes are created
- Check connection pool settings
- Monitor database query performance

## Security Notes

⚠️ **Important**: This application currently has:
- CSRF disabled (acceptable for API-only, but consider enabling for production)
- No authentication (all endpoints are public)
- CORS allows all origins (restrict in production to your frontend domain)

For production, consider:
- Adding authentication (JWT/OAuth2)
- Restricting CORS to specific domains
- Enabling rate limiting
- Adding request validation

