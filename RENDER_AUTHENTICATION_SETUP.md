# Render Authentication Setup Guide

## Complete Setup Instructions for Authentication System

### 1. Environment Variables Required in Render

Add these environment variables in your Render dashboard:

#### Database (Already configured)
- `SPRING_DATASOURCE_URL` - PostgreSQL connection URL
- `SPRING_DATASOURCE_USERNAME` - Database username
- `SPRING_DATASOURCE_PASSWORD` - Database password

#### JWT Configuration
- `JWT_SECRET` - **REQUIRED**: A secure random string (minimum 32 characters)
  - Generate with: `openssl rand -base64 32`
  - Or use: https://www.random.org/strings/?num=1&len=32&digits=on&upperalpha=on&loweralpha=on&unique=on&format=html&rnd=new
  - **IMPORTANT**: Keep this secret! Never commit to git.

- `JWT_EXPIRATION` - Optional (default: 86400000 = 24 hours in milliseconds)

#### Email Configuration (SMTP)
Choose one option:

**Option A: Gmail (Recommended for testing)**
- `MAIL_HOST=smtp.gmail.com`
- `MAIL_PORT=587`
- `MAIL_USERNAME=your-email@gmail.com`
- `MAIL_PASSWORD=your-app-password` (Use App Password, not regular password)
- `MAIL_FROM=noreply@commuterapp.com`
- `APP_BASE_URL=https://your-app.onrender.com`

**Option B: SendGrid (Recommended for production)**
- `MAIL_HOST=smtp.sendgrid.net`
- `MAIL_PORT=587`
- `MAIL_USERNAME=apikey`
- `MAIL_PASSWORD=your-sendgrid-api-key`
- `MAIL_FROM=noreply@commuterapp.com`
- `APP_BASE_URL=https://your-app.onrender.com`

**Option C: Mailgun**
- `MAIL_HOST=smtp.mailgun.org`
- `MAIL_PORT=587`
- `MAIL_USERNAME=your-mailgun-username`
- `MAIL_PASSWORD=your-mailgun-password`
- `MAIL_FROM=noreply@yourdomain.com`
- `APP_BASE_URL=https://your-app.onrender.com`

#### Application Configuration
- `APP_NAME=Commuter Reviews` (Optional)
- `APP_BASE_URL` - Your Render app URL (e.g., `https://your-app.onrender.com`)
- `SPRING_PROFILES_ACTIVE=prod` (Optional, already set in Dockerfile)

### 2. Database Migration

Run this SQL script on your PostgreSQL database:

```sql
-- Create users table
CREATE TABLE IF NOT EXISTS users (
    id BIGSERIAL PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(100) NOT NULL UNIQUE,
    password_hash VARCHAR(255) NOT NULL,
    commuter_id BIGINT NOT NULL UNIQUE,
    email_verified BOOLEAN NOT NULL DEFAULT FALSE,
    verification_token VARCHAR(100),
    verification_token_expires TIMESTAMP,
    password_reset_token VARCHAR(100),
    password_reset_token_expires TIMESTAMP,
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);

-- Add indexes
CREATE INDEX IF NOT EXISTS idx_users_username ON users(username);
CREATE INDEX IF NOT EXISTS idx_users_email ON users(email);
CREATE INDEX IF NOT EXISTS idx_users_commuter_id ON users(commuter_id);
CREATE INDEX IF NOT EXISTS idx_users_verification_token ON users(verification_token);
CREATE INDEX IF NOT EXISTS idx_users_password_reset_token ON users(password_reset_token);
```

### 3. Gmail App Password Setup (If using Gmail)

1. Go to your Google Account settings
2. Enable 2-Step Verification
3. Go to App Passwords: https://myaccount.google.com/apppasswords
4. Generate a new app password for "Mail"
5. Use this 16-character password as `MAIL_PASSWORD`

### 4. SendGrid Setup (Recommended for Production)

1. Sign up at https://sendgrid.com
2. Create an API Key in Settings > API Keys
3. Use `apikey` as `MAIL_USERNAME`
4. Use your API key as `MAIL_PASSWORD`
5. Verify your sender email in SendGrid

### 5. Testing Email Configuration

After deployment, test by:
1. Registering a new account
2. Check email inbox for verification email
3. Click verification link
4. Try logging in

### 6. Security Checklist

- [ ] `JWT_SECRET` is set and is at least 32 characters
- [ ] `JWT_SECRET` is unique (not shared with other apps)
- [ ] Email credentials are set correctly
- [ ] `APP_BASE_URL` matches your Render app URL
- [ ] Database migration has been run
- [ ] All environment variables are set in Render dashboard

### 7. Troubleshooting

**Email not sending:**
- Check SMTP credentials
- Verify firewall/network allows SMTP
- Check Render logs for email errors
- Test with a simple email service first (Gmail)

**Authentication not working:**
- Verify JWT_SECRET is set
- Check that token is being sent in Authorization header
- Check Render logs for authentication errors

**Database errors:**
- Ensure users table exists
- Check database connection
- Verify all migrations are run

### 8. API Endpoints

**Public Endpoints:**
- `POST /api/auth/register` - Register new user
- `POST /api/auth/login` - Login
- `GET /api/auth/verify-email?token=...` - Verify email
- `POST /api/auth/forgot-password` - Request password reset
- `POST /api/auth/reset-password` - Reset password
- `POST /api/auth/resend-verification` - Resend verification email

**Protected Endpoints (Require JWT token):**
- `POST /api/reviews` - Create review
- `PUT /api/reviews/{id}` - Update review
- `DELETE /api/reviews/{id}` - Delete review
- `POST /api/reviews/{id}/helpful` - Mark helpful
- `DELETE /api/reviews/{id}/helpful` - Unmark helpful

**Public Read Endpoints:**
- `GET /api/routes/{routeId}/reviews` - List reviews
- `GET /api/routes/{routeId}/rating` - Get rating summary
- `GET /api/routes/{routeId}/statistics` - Get statistics
- `GET /api/categories` - Get categories

### 9. Frontend Pages

- `/login.html` - Login page
- `/register.html` - Registration page
- `/forgot-password.html` - Forgot password
- `/reset-password.html?token=...` - Reset password
- `/verify-email.html?token=...` - Email verification
- `/` - Main reviews page (with login/logout)

### 10. Quick Start Checklist

1. ✅ Set all environment variables in Render
2. ✅ Run database migration script
3. ✅ Deploy application
4. ✅ Test registration
5. ✅ Check email for verification
6. ✅ Test login
7. ✅ Test creating a review
8. ✅ Test password reset flow

## Important Notes

- **JWT_SECRET**: Must be set before first deployment
- **Email**: Must be configured for email verification to work
- **APP_BASE_URL**: Must match your Render URL for email links to work
- **Database**: Users table must exist before users can register

