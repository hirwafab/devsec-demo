# Django User Authentication Service (UAS) - hirwafab

## Overview

This is a complete User Authentication Service (UAS) implemented as a Django app named `hirwafab`. The application provides a full authentication workflow including registration, login, logout, password management, and profile management.

## Features

### Core Authentication Flows
✅ **User Registration** - Create new accounts with email validation and password strength requirements
✅ **User Login** - Secure authentication with session management
✅ **User Logout** - Graceful session termination
✅ **Protected Dashboard** - Authenticated-only area for logged-in users
✅ **Password Change** - Secure password updates for authenticated users
✅ **User Profile** - View and edit user information and profile settings

### Security Features
- **CSRF Protection** - All forms include CSRF tokens
- **Password Hashing** - Django's built-in password hashing with PBKDF2
- **Input Validation** - Both client-side and server-side validation
- **Secure Authentication** - Uses Django's built-in authentication system
- **Session Management** - Secure session handling via Django middleware
- **Login Required Decorators** - Protected views require authentication
- **Email Uniqueness** - Prevents duplicate email registrations
- **Password Strength** - Django's password validators enforced:
  - Minimum 8 characters
  - Common password checking
  - Numeric-only password prevention
  - User attribute similarity checking

## Project Structure

```
hirwafab/
├── migrations/
│   ├── __init__.py
│   └── 0001_initial.py
├── templates/
│   └── hirwafab/
│       ├── base.html              (Base template with styling)
│       ├── register.html
│       ├── login.html
│       ├── logout_confirm.html
│       ├── dashboard.html
│       ├── profile.html
│       └── change_password.html
├── __init__.py
├── admin.py                        (Django admin configuration)
├── apps.py                         (App configuration)
├── forms.py                        (Django forms)
├── models.py                       (Database models)
├── tests.py                        (Comprehensive test suite)
├── urls.py                         (URL routing)
└── views.py                        (View functions)
```

## Models

### UserProfile
Extends Django's built-in `User` model with additional fields:
- `user` - OneToOneField relationship with User model
- `bio` - Text field for user biography (max 500 characters)
- `profile_picture` - Image field for profile photo
- `created_at` - Auto-populated creation timestamp
- `updated_at` - Auto-populated last update timestamp

Database table: `hirwafab_user_profile`

## URL Endpoints

| URL | Name | View | Method | Authentication Required |
|-----|------|------|--------|------------------------|
| `/register/` | `hirwafab:register` | `register` | GET, POST | No |
| `/login/` | `hirwafab:login` | `login_view` | GET, POST | No |
| `/logout/` | `hirwafab:logout` | `logout_view` | GET, POST | Yes |
| `/dashboard/` | `hirwafab:dashboard` | `dashboard` | GET | Yes |
| `/profile/` | `hirwafab:profile` | `profile` | GET, POST | Yes |
| `/change-password/` | `hirwafab:change_password` | `change_password` | GET, POST | Yes |

## Forms

### RegistrationForm (UserCreationForm)
- Extends Django's UserCreationForm
- Fields: username, email, password1, password2
- Validation: 
  - Email uniqueness check
  - Username uniqueness check
  - Password match requirement
  - Django password validators
- Auto-creates UserProfile on successful registration

### LoginForm
- Fields: username, password
- Simple authentication form with custom styling

### PasswordChangeForm
- Extends Django's PasswordChangeForm
- Fields: old_password, new_password1, new_password2
- Validates current password before allowing change
- Enforces Django password validators

### UserProfileForm
- Model form for UserProfile
- User fields: first_name, last_name, email
- Profile fields: bio, profile_picture
- Auto-updates associated User model on save

## Views

### Authentication Views
- `register()` - Handle user registration with validation and profile creation
- `login_view()` - Handle user login with credential verification
- `logout_view()` - Handle user logout with confirmation

### Protected Views (Require @login_required)
- `dashboard()` - Display welcome and user information
- `profile()` - View and edit user profile
- `change_password()` - Manage password changes

All views include:
- CSRF protection via `@csrf_protect`
- HTTP method restrictions via `@require_http_methods`
- User feedback via Django messages framework
- Error handling and validation messages

## Templates

### base.html
- Responsive base template with embedded CSS
- Mobile-friendly design with Flexbox
- Navigation bar for authenticated users
- Message display system
- Form styling and error display

### Authentication Templates
- **register.html** - Registration form with password hints
- **login.html** - Simple login form
- **logout_confirm.html** - Confirmation before logout

### Protected Area Templates
- **dashboard.html** - Welcome area with profile info and navigation
- **profile.html** - Edit user profile and information
- **change_password.html** - Secure password change form

All templates include:
- Consistent styling via base.html
- CSRF tokens in all forms
- Error message display
- Success feedback messages
- Responsive design

## Admin Interface

The `UserProfile` model is registered with Django admin:
- List view: Shows username, created_at, updated_at
- Search: By username, email, and bio
- Filter: By creation and update dates
- Read-only: Timestamps
- Organized fieldsets for better UX

Access via: `/admin/`

## Security Best Practices Implemented

1. **Uses Django's Built-in Authentication**
   - Leverages tested, production-ready user authentication
   - PBKDF2 password hashing with SHA256
   - Session-based authentication

2. **CSRF Protection**
   - All forms include CSRF tokens
   - `@csrf_protect` decorator on views
   - CsrfViewMiddleware enabled in settings

3. **Input Validation**
   - Server-side form validation in all forms
   - Custom validators for email and username uniqueness
   - Django's password validators enforced
   - HTML5 input types for client-side hints

4. **Access Control**
   - `@login_required` decorators protect sensitive views
   - Unauthenticated users redirected to login
   - Authenticated users redirected from register/login pages

5. **Error Handling**
   - Graceful error messages without exposing sensitive info
   - Secure password change validation
   - User-friendly validation error feedback

6. **Session Management**
   - Django's session middleware
   - Secure session handling
   - Proper logout clears session

## Testing

The app includes a comprehensive test suite with 33 tests covering:

### Test Categories

**User Registration Tests (7 tests)**
- Registration page loads
- Successful user registration
- Duplicate username prevention
- Duplicate email prevention
- Password mismatch validation
- Weak password rejection
- Authenticated user redirect

**User Login Tests (5 tests)**
- Login page loads
- Valid credential authentication
- Invalid username rejection
- Invalid password rejection
- Authenticated user redirect

**User Logout Tests (3 tests)**
- Logout requires authentication
- Confirmation page loads
- Session cleared on logout

**Dashboard Tests (3 tests)**
- Dashboard requires authentication
- Authenticated access
- Auto-profile creation

**Password Change Tests (5 tests)**
- Requires authentication
- Page loads successfully
- Successful password change
- Wrong old password rejection
- Password mismatch rejection

**Profile Tests (4 tests)**
- Requires authentication
- Page loads successfully
- Successful profile update
- CSRF protection

**URL Configuration Tests (6 tests)**
- All URL names resolve correctly
- Correct URL patterns

### Running Tests

```bash
# Run all hirwafab tests
python manage.py test hirwafab

# Run with verbose output
python manage.py test hirwafab --verbosity=2

# Run specific test class
python manage.py test hirwafab.tests.UserRegistrationTests

# Run specific test
python manage.py test hirwafab.tests.UserRegistrationTests.test_register_user_successfully
```

### Test Results
All 33 tests pass successfully, covering:
- ✅ Core authentication flows
- ✅ Error cases and validation
- ✅ Access control
- ✅ Session management
- ✅ URL configuration

## Setup and Installation

### Prerequisites
- Django 6.0.4+
- Python 3.8+
- SQLite3 (or other configured database)

### Installation Steps

1. **Create the app** (Already done)
   ```bash
   python manage.py startapp hirwafab
   ```

2. **Add to INSTALLED_APPS** in `settings.py`
   ```python
   INSTALLED_APPS = [
       ...
       'hirwafab',
   ]
   ```

3. **Include app URLs** in project `urls.py`
   ```python
   urlpatterns = [
       ...
       path('', include('hirwafab.urls')),
   ]
   ```

4. **Run migrations**
   ```bash
   python manage.py makemigrations
   python manage.py migrate
   ```

5. **Test the app**
   ```bash
   python manage.py test hirwafab
   ```

6. **Create superuser** (Optional, for admin access)
   ```bash
   python manage.py createsuperuser
   ```

7. **Run development server**
   ```bash
   python manage.py runserver
   ```

### Access Points
- Register: http://localhost:8000/register/
- Login: http://localhost:8000/login/
- Dashboard: http://localhost:8000/dashboard/ (requires login)
- Profile: http://localhost:8000/profile/ (requires login)
- Admin: http://localhost:8000/admin/ (requires superuser)

## Usage Flow

### New User Flow
1. Go to `/register/`
2. Enter username, email, and password
3. Click "Create Account"
4. Redirected to login page
5. Enter credentials to login
6. Access `/dashboard/` (protected area)

### Returning User Flow
1. Go to `/login/`
2. Enter username and password
3. Click "Log In"
4. Access `/dashboard/` and other protected pages

### Profile Management
1. From dashboard, click "Edit Profile"
2. Update first name, last name, email, bio, or profile picture
3. Click "Save Changes"
4. Changes reflected on dashboard

### Password Management
1. From dashboard, click "Change Password"
2. Enter current password and new password
3. Click "Change Password"
4. Can login with new password

### Logout
1. From dashboard, click "Logout"
2. Confirm logout
3. Redirected to login page
4. No longer can access protected pages

## Django Best Practices Followed

1. **Separation of Concerns**
   - Forms in `forms.py`
   - Models in `models.py`
   - Views in `views.py`
   - URLs in `urls.py`
   - Templates in `templates/`

2. **DRY Principle**
   - Reused Django's UserCreationForm
   - Reused Django's PasswordChangeForm
   - Created base template for consistent styling
   - Forms include field validation to avoid duplication

3. **Clean Code**
   - Meaningful variable and function names
   - Comprehensive docstrings
   - Consistent formatting
   - Comments for complex logic

4. **Security**
   - No hardcoded secrets
   - CSRF protection
   - SQL injection prevention (ORM usage)
   - XSS prevention (template escaping)
   - Secure password handling

5. **Testing**
   - Comprehensive test coverage
   - Tests for success and failure cases
   - Integration tests for workflows
   - Tests for access control

6. **Admin Integration**
   - Model registered with admin
   - Customized admin interface
   - Fieldsets for organization
   - Search and filtering

## Environment Configuration

The project uses environment variables configured in `.env`:
```
DJANGO_SECRET_KEY=your-secret-key-here
DJANGO_DEBUG=True
```

Settings pulled from environment in `settings.py`:
```python
SECRET_KEY = os.environ.get('DJANGO_SECRET_KEY')
DEBUG = os.environ.get('DJANGO_DEBUG')
```

## Database

**Default**: SQLite3 at `db.sqlite3`

The app works with any Django-supported database:
- PostgreSQL
- MySQL
- Oracle
- MariaDB
- SQLite (default)

Model uses:
- `BigAutoField` for primary key (auto-increment)
- Standard Django ORM relationships
- No database-specific features

## Future Enhancements

Potential additions (out of scope for this assignment):
- Email verification for new registrations
- Password reset via email
- Two-factor authentication
- Social authentication (OAuth)
- User permission levels
- API endpoints
- Rate limiting on login attempts
- Account deactivation
- Activity logging
- Email notifications

## Troubleshooting

### Tables Not Created
```bash
python manage.py makemigrations hirwafab
python manage.py migrate
```

### Static Files Not Loading
```bash
python manage.py collectstatic
```

### Template Not Found
- Ensure `APP_DIRS: True` in TEMPLATES setting
- Check templates are in `hirwafab/templates/hirwafab/`

### CSRF Token Missing
- Ensure `CsrfViewMiddleware` is enabled
- Include `{% csrf_token %}` in all POST forms

### Login Not Working
- Verify user exists: `python manage.py dbshell` → `SELECT * FROM auth_user;`
- Check password hash is being set correctly
- Verify session middleware is enabled

## Files Modified/Created

### Created Files
- `hirwafab/__init__.py`
- `hirwafab/apps.py`
- `hirwafab/models.py`
- `hirwafab/forms.py`
- `hirwafab/views.py`
- `hirwafab/urls.py`
- `hirwafab/admin.py`
- `hirwafab/tests.py`
- `hirwafab/migrations/__init__.py`
- `hirwafab/migrations/0001_initial.py`
- `hirwafab/templates/hirwafab/base.html`
- `hirwafab/templates/hirwafab/register.html`
- `hirwafab/templates/hirwafab/login.html`
- `hirwafab/templates/hirwafab/logout_confirm.html`
- `hirwafab/templates/hirwafab/dashboard.html`
- `hirwafab/templates/hirwafab/profile.html`
- `hirwafab/templates/hirwafab/change_password.html`

### Modified Files
- `devsec_demo/settings.py` - Added 'hirwafab' to INSTALLED_APPS
- `devsec_demo/urls.py` - Included hirwafab URLs

## Deployment Considerations

For production deployment:
1. Set `DEBUG = False` in environment
2. Set `ALLOWED_HOSTS` properly
3. Use strong `SECRET_KEY`
4. Enable HTTPS/SSL
5. Use production database (PostgreSQL recommended)
6. Configure email backend for password reset
7. Set up static files serving (e.g., AWS S3)
8. Use environment variables for secrets
9. Enable security headers
10. Set up logging and monitoring

## License

This project is part of DevSec training materials.

## Support

For issues or questions about the implementation:
1. Check tests for usage examples
2. Review Django documentation
3. Check template rendering in debug mode
4. Verify environment configuration

---

**Implementation Date**: April 14, 2026
**Student**: hirwafab
**Django Version**: 6.0.4
**Python Version**: 3.8+
