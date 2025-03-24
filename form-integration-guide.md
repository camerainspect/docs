# Contact Form Integration Guide

This document provides a comprehensive guide for integrating the CameraInspect contact form with the PHP backend. The integration enables visitors to send inquiries that are processed, validated, and delivered to the appropriate team members.

## Overview

The contact form system consists of three main components:

1. **HTML Form** - The user interface for collecting visitor information
2. **JavaScript Handler** - Client-side validation and form submission via AJAX
3. **PHP Backend** - Server-side processing, validation, and email delivery

## HTML Form Structure

The contact form is structured using HTML5 with proper input types and validation attributes:

```html
<div class="contact-form">
    <h3>Wyślij wiadomość</h3>
    <form id="contact-form">
        <div class="form-row">
            <div class="form-group">
                <label for="name">Imię i nazwisko</label>
                <input type="text" id="name" class="form-control" required>
            </div>
            <div class="form-group">
                <label for="email">Email</label>
                <input type="email" id="email" class="form-control" required>
            </div>
        </div>
        <div class="form-row">
            <div class="form-group">
                <label for="phone">Telefon</label>
                <input type="tel" id="phone" class="form-control">
            </div>
            <div class="form-group">
                <label for="subject">Temat</label>
                <select id="subject" class="form-control">
                    <option value="info">Informacja ogólna</option>
                    <option value="demo">Zamówienie demonstracji</option>
                    <option value="quote">Zapytanie o wycenę</option>
                    <option value="support">Wsparcie techniczne</option>
                    <option value="other">Inne</option>
                </select>
            </div>
        </div>
        <div class="form-group">
            <label for="message">Wiadomość</label>
            <textarea id="message" class="form-control" required></textarea>
        </div>
        <button type="submit" class="btn btn-primary">Wyślij wiadomość</button>
    </form>
</div>
```

### Key Form Elements

- **Form ID**: `contact-form` - Used by JavaScript to attach event handlers
- **Required Fields**: name, email, message - Enforced by both client and server validation
- **Subject Selection**: Pre-defined options that determine routing of the message
- **Submit Button**: Triggers the form submission process

## JavaScript Integration

The JavaScript handler (`contact-form.js`) handles user interaction, form validation, and AJAX submission:

### Including the JavaScript

Add the following to your HTML file, ideally just before the closing `</body>` tag:

```html
<script src="js/contact-form.js"></script>
```

### Key JavaScript Functions

- **Form Initialization**: Sets up event listeners when the DOM is ready
- **Validation**: Checks for required fields and proper formats before submission
- **Form Submission**: Sends data via AJAX to the backend
- **User Feedback**: Shows loading indicators, success messages, or error information

### Customizing Validation

The validation rules can be customized in the `validationConfig` object:

```javascript
// In contact-form.js
const validationConfig = {
    name: {
        required: true,
        minLength: 2,
        maxLength: 100,
        errorMessage: 'Please enter your name (2-100 characters)'
    },
    // Other field configurations...
};
```

### Extending Functionality

To add custom validation or functionality:

1. Locate the `initContactForm` function in the JavaScript file
2. Add additional event listeners or modify the validation rules
3. Update the form processing logic as needed

## PHP Backend Integration

The PHP backend script (`contact_form_handler.php`) processes form submissions, validates input, and sends emails:

### Backend Setup

1. Place the `contact_form_handler.php` file in your server's `backend` directory
2. Ensure the server has PHP installed (version 7.4+ recommended)
3. Configure your web server to handle PHP files correctly

### Configuration

Edit the `$config` array in the PHP file to customize behavior:

```php
// In contact_form_handler.php
$config = [
    'admin_email' => 'info@camerainspect.com',
    'support_email' => 'support@camerainspect.com',
    'sales_email' => 'sales@camerainspect.com',
    'demo_email' => 'demo@camerainspect.com',
    'debug_mode' => false, // Set to true for debugging
    'recaptcha_secret' => 'YOUR_RECAPTCHA_SECRET_KEY', // Add your key here
    'enable_recaptcha' => false, // Set to true to enable reCAPTCHA
    'log_file' => 'contact_form_logs.txt' // Log file path
];
```

### Email Customization

To customize the email templates:

1. Locate the HTML email templates in the `$email_body` and `$confirmation_body` variables
2. Modify the HTML structure and content to match your branding
3. Update the email headers if needed

### Database Integration

The PHP script includes commented code for database integration. To enable it:

1. Uncomment the database code section
2. Update the database connection parameters
3. Ensure the table structure matches your database schema

Example table structure:

```sql
CREATE TABLE contact_submissions (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL,
    phone VARCHAR(20),
    subject VARCHAR(50) NOT NULL,
    message TEXT NOT NULL,
    ip VARCHAR(45) NOT NULL,
    created_at DATETIME NOT NULL
);
```

### Security Features

The backend includes several security features:

- **Input Validation**: All user input is validated and sanitized
- **CORS Protection**: Configurable Cross-Origin Resource Sharing
- **Rate Limiting**: Basic protection against form spam (can be extended)
- **reCAPTCHA Integration**: Optional Google reCAPTCHA v3 support

#### Adding reCAPTCHA

To enable reCAPTCHA protection:

1. Register for Google reCAPTCHA and get your site and secret keys
2. Add the reCAPTCHA script to your HTML:

```html
<script src="https://www.google.com/recaptcha/api.js" async defer></script>
```

3. Add the reCAPTCHA element to your form:

```html
<div class="g-recaptcha" data-sitekey="YOUR_SITE_KEY"></div>
```

4. Update the `contact_form_handler.php` configuration:

```php
$config = [
    // Other settings...
    'recaptcha_secret' => 'YOUR_RECAPTCHA_SECRET_KEY',
    'enable_recaptcha' => true
];
```

## Testing and Troubleshooting

### Testing the Integration

1. Fill out the form with test data and submit
2. Check the server logs for any errors
3. Verify that emails are being sent correctly
4. Test validation by submitting invalid data

### Debug Mode

Enable debug mode in the PHP configuration for detailed logging:

```php
$config = [
    // Other settings...
    'debug_mode' => true
];
```

This will:
- Log detailed information about each submission
- Include additional debug information in the JSON response
- Write to the log file specified in the configuration

### Common Issues and Solutions

#### Form Submission Not Working

- Check browser console for JavaScript errors
- Verify that the form ID matches what's expected in the JavaScript
- Ensure the path to the PHP handler is correct in the JavaScript

#### Emails Not Being Sent

- Check server logs for PHP mail() function errors
- Verify that your server is configured to send emails
- Test with alternative email addresses
- Check spam folders for test emails

#### Validation Errors

- Review the validation rules in both JavaScript and PHP
- Ensure that required fields match between front and backend
- Test with various input formats to ensure validation is working

#### CORS Issues

If you're experiencing Cross-Origin Resource Sharing (CORS) issues:

1. Update the CORS headers in the PHP file to match your domain:

```php
header('Access-Control-Allow-Origin: https://your-domain.com');
```

2. For development, you can temporarily allow all origins:

```php
header('Access-Control-Allow-Origin: *');
```

## Advanced Customization

### Adding Custom Fields

To add new fields to the form:

1. Add the HTML input element to the form
2. Update the JavaScript validation configuration
3. Modify the PHP backend to process the new field
4. Update the email templates to include the new information

Example of adding a "Company" field:

```html
<!-- In HTML -->
<div class="form-group">
    <label for="company">Company</label>
    <input type="text" id="company" class="form-control">
</div>
```

```javascript
// In JavaScript validation config
company: {
    required: false,
    maxLength: 100,
    errorMessage: 'Company name should be
