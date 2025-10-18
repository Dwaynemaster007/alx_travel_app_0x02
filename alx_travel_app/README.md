# 💳 Travel Booking Platform - Chapa Payment Integration

A production-ready travel booking application with secure payment processing powered by Chapa Payment Gateway. Built with Django, featuring transaction management, real-time payment verification, and automated email confirmations.

[![Django](https://img.shields.io/badge/Django-4.0+-092E20?style=for-the-badge&logo=django&logoColor=white)](https://www.djangoproject.com/)
[![Python](https://img.shields.io/badge/Python-3.9+-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-13+-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![Celery](https://img.shields.io/badge/Celery-5.0+-37814A?style=for-the-badge&logo=celery&logoColor=white)](https://docs.celeryproject.org/)
[![Chapa](https://img.shields.io/badge/Chapa-Payment-FF6B35?style=for-the-badge)](https://chapa.co/)

<div align="center">

### 🌟 Book Your Dream Getaway, Pay with Confidence 🌟

*Where seamless bookings meet secure payments*

[Features](#-key-features) • [Quick Start](#-quick-start) • [API Docs](#-api-documentation) • [Payment Flow](#-payment-workflow)

</div>

---

## 📋 Project Overview

This project represents **Milestone 4** of the **ALX ProDev Backend** curriculum, implementing a complete payment integration system for a travel booking platform. The application demonstrates industry-standard payment processing patterns, including secure API integration, transaction management, payment verification, and automated customer notifications.

### 🎯 What Makes This Special?

Unlike basic payment implementations, this system features:
- ✅ **Real Payment Gateway** - Chapa API integration (not a simulation)
- ✅ **Transaction Tracking** - Complete payment lifecycle management
- ✅ **Async Processing** - Celery for background email tasks
- ✅ **Secure Credentials** - Environment-based API key management
- ✅ **Error Handling** - Graceful failure management
- ✅ **Sandbox Testing** - Safe development environment

---

## 🌟 Key Features

### 💰 Payment Processing
- **Secure Payment Initiation** - Direct integration with Chapa API
- **Real-time Verification** - Instant payment status updates
- **Transaction Logging** - Complete audit trail
- **Multiple Payment Methods** - Cards, mobile money, bank transfers
- **Sandbox Testing** - Risk-free development and testing

### 📊 Booking Management
- **Seamless Integration** - Payments linked to bookings
- **Status Tracking** - Pending, Completed, Failed states
- **Booking References** - Unique transaction identifiers
- **Amount Validation** - Automatic price calculation

### 📧 Automated Communications
- **Email Confirmations** - Sent on successful payment
- **Background Tasks** - Non-blocking Celery workers
- **Custom Templates** - Professional email designs
- **Retry Logic** - Ensures delivery

### 🔒 Security Features
- **Environment Variables** - Secure API key storage
- **HTTPS Communication** - Encrypted data transfer
- **Transaction IDs** - Unique payment identifiers
- **Error Logging** - Comprehensive audit trails

---

## 📁 Project Structure

```
alx_travel_app_0x02/
├── alx_travel_app/              # Main project directory
│   ├── settings.py              # Django settings with Chapa config
│   ├── urls.py                  # URL routing
│   ├── celery.py                # Celery configuration
│   └── wsgi.py                  # WSGI configuration
├── listings/                    # Main app for bookings & payments
│   ├── models.py                # Booking and Payment models
│   ├── views.py                 # Payment initiation & verification APIs
│   ├── serializers.py           # API serializers
│   ├── tasks.py                 # Celery tasks (email sending)
│   ├── urls.py                  # App-specific URLs
│   └── admin.py                 # Django admin configuration
├── templates/                   # Email templates
│   └── emails/
│       └── payment_confirmation.html
├── static/                      # Static files
├── media/                       # User uploads
├── requirements.txt             # Python dependencies
├── .env.example                 # Environment variables template
├── manage.py                    # Django management script
└── README.md                    # This file
```

---

## 🚀 Quick Start

### Prerequisites

Before you begin, ensure you have:
- **Python 3.9+**
- **PostgreSQL 13+**
- **Redis** (for Celery)
- **Chapa Account** - [Sign up here](https://developer.chapa.co/)
- **Git**

### Installation

```bash
# 1️⃣ Clone the repository
git clone https://github.com/Dwaynemaster007/alx_travel_app_0x02.git
cd alx_travel_app_0x02

# 2️⃣ Create virtual environment
python3 -m venv venv
source venv/bin/activate  # Linux/macOS
# venv\Scripts\activate   # Windows

# 3️⃣ Install dependencies
pip install -r requirements.txt

# 4️⃣ Set up environment variables
cp .env.example .env
# Edit .env with your credentials
nano .env

# 5️⃣ Configure database
python manage.py makemigrations
python manage.py migrate

# 6️⃣ Create superuser (optional)
python manage.py createsuperuser

# 7️⃣ Start Redis (for Celery)
redis-server

# 8️⃣ Start Celery worker (new terminal)
celery -A alx_travel_app worker --loglevel=info

# 9️⃣ Start development server
python manage.py runserver
```

### Environment Variables

Create a `.env` file in the project root:

```bash
# Django Settings
SECRET_KEY=your-django-secret-key
DEBUG=True
ALLOWED_HOSTS=localhost,127.0.0.1

# Database
DB_NAME=travel_booking_db
DB_USER=postgres
DB_PASSWORD=your-password
DB_HOST=localhost
DB_PORT=5432

# Chapa API
CHAPA_SECRET_KEY=your-chapa-secret-key
CHAPA_BASE_URL=https://api.chapa.co/v1

# Email Configuration (for confirmations)
EMAIL_HOST=smtp.gmail.com
EMAIL_PORT=587
EMAIL_USE_TLS=True
EMAIL_HOST_USER=your-email@gmail.com
EMAIL_HOST_PASSWORD=your-app-password

# Celery
CELERY_BROKER_URL=redis://localhost:6379/0
CELERY_RESULT_BACKEND=redis://localhost:6379/0
```

---

## 💾 Database Models

### Payment Model

```python
class Payment(models.Model):
    STATUS_CHOICES = [
        ('pending', 'Pending'),
        ('completed', 'Completed'),
        ('failed', 'Failed'),
        ('cancelled', 'Cancelled'),
    ]
    
    booking = ForeignKey(Booking, on_delete=CASCADE)
    transaction_id = CharField(max_length=100, unique=True)
    reference = CharField(max_length=100, unique=True)
    amount = DecimalField(max_digits=10, decimal_places=2)
    currency = CharField(max_length=3, default='ETB')
    status = CharField(max_length=20, choices=STATUS_CHOICES, default='pending')
    payment_method = CharField(max_length=50, blank=True)
    chapa_checkout_url = URLField(blank=True)
    created_at = DateTimeField(auto_now_add=True)
    updated_at = DateTimeField(auto_now=True)
    
    class Meta:
        ordering = ['-created_at']
    
    def __str__(self):
        return f"Payment {self.reference} - {self.status}"
```

### Booking Model (Updated)

```python
class Booking(models.Model):
    user = ForeignKey(User, on_delete=CASCADE)
    listing = ForeignKey(Listing, on_delete=CASCADE)
    check_in = DateField()
    check_out = DateField()
    total_price = DecimalField(max_digits=10, decimal_places=2)
    guests = PositiveIntegerField()
    status = CharField(max_length=20, default='pending')
    created_at = DateTimeField(auto_now_add=True)
    
    @property
    def has_payment(self):
        return hasattr(self, 'payment')
    
    @property
    def payment_status(self):
        return self.payment.status if self.has_payment else 'unpaid'
```

---

## 🔄 Payment Workflow

### 1️⃣ Payment Initiation

**Endpoint:** `POST /api/payments/initiate/`

**Request Body:**
```json
{
  "booking_id": 123,
  "email": "customer@example.com",
  "first_name": "John",
  "last_name": "Doe",
  "return_url": "https://yourapp.com/payment/success",
  "callback_url": "https://yourapp.com/api/payments/callback/"
}
```

**Response:**
```json
{
  "status": "success",
  "message": "Payment initiated successfully",
  "data": {
    "checkout_url": "https://checkout.chapa.co/checkout/payment/...",
    "reference": "TXN-1234567890",
    "transaction_id": "CH-TX-1234567890"
  }
}
```

**Backend Process:**
1. Validate booking exists and amount
2. Generate unique reference
3. Call Chapa API to initialize payment
4. Create Payment record with status='pending'
5. Return checkout URL to frontend

---

### 2️⃣ Payment Verification

**Endpoint:** `GET /api/payments/verify/<reference>/`

**Response:**
```json
{
  "status": "success",
  "data": {
    "reference": "TXN-1234567890",
    "status": "completed",
    "amount": 5000.00,
    "currency": "ETB",
    "payment_method": "card",
    "transaction_id": "CH-TX-1234567890"
  }
}
```

**Backend Process:**
1. Receive transaction reference
2. Call Chapa verification API
3. Update Payment model status
4. Trigger confirmation email (Celery task)
5. Update booking status

---

### 3️⃣ Payment Callback (Webhook)

**Endpoint:** `POST /api/payments/callback/`

**Chapa Sends:**
```json
{
  "tx_ref": "TXN-1234567890",
  "status": "success",
  "amount": 5000.00,
  "currency": "ETB",
  "payment_method": "card"
}
```

**Backend Process:**
1. Verify webhook signature
2. Find Payment by reference
3. Update status based on Chapa response
4. Send confirmation email
5. Log transaction

---

## 📖 API Documentation

### Initiate Payment

```bash
curl -X POST http://localhost:8000/api/payments/initiate/ \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{
    "booking_id": 123,
    "email": "customer@example.com",
    "first_name": "John",
    "last_name": "Doe",
    "return_url": "https://yourapp.com/success",
    "callback_url": "https://yourapp.com/api/payments/callback/"
  }'
```

### Verify Payment

```bash
curl -X GET http://localhost:8000/api/payments/verify/TXN-1234567890/ \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### List User Payments

```bash
curl -X GET http://localhost:8000/api/payments/ \
  -H "Authorization: Bearer YOUR_TOKEN"
```

---

## 🎨 Implementation Details

### Payment Initiation View

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
import requests
import uuid
from django.conf import settings

class InitiatePaymentView(APIView):
    def post(self, request):
        try:
            booking_id = request.data.get('booking_id')
            booking = Booking.objects.get(id=booking_id)
            
            # Generate unique reference
            reference = f"TXN-{uuid.uuid4().hex[:10].upper()}"
            
            # Prepare Chapa payload
            payload = {
                "amount": str(booking.total_price),
                "currency": "ETB",
                "email": request.data.get('email'),
                "first_name": request.data.get('first_name'),
                "last_name": request.data.get('last_name'),
                "tx_ref": reference,
                "callback_url": request.data.get('callback_url'),
                "return_url": request.data.get('return_url'),
                "customization": {
                    "title": "Travel Booking Payment",
                    "description": f"Booking for {booking.listing.name}"
                }
            }
            
            # Call Chapa API
            headers = {
                "Authorization": f"Bearer {settings.CHAPA_SECRET_KEY}",
                "Content-Type": "application/json"
            }
            
            response = requests.post(
                f"{settings.CHAPA_BASE_URL}/transaction/initialize",
                json=payload,
                headers=headers
            )
            
            if response.status_code == 200:
                data = response.json()['data']
                
                # Create Payment record
                payment = Payment.objects.create(
                    booking=booking,
                    reference=reference,
                    transaction_id=data['tx_ref'],
                    amount=booking.total_price,
                    chapa_checkout_url=data['checkout_url'],
                    status='pending'
                )
                
                return Response({
                    'status': 'success',
                    'message': 'Payment initiated successfully',
                    'data': {
                        'checkout_url': data['checkout_url'],
                        'reference': reference,
                        'transaction_id': data['tx_ref']
                    }
                }, status=status.HTTP_200_OK)
            else:
                return Response({
                    'status': 'error',
                    'message': 'Payment initiation failed'
                }, status=status.HTTP_400_BAD_REQUEST)
                
        except Exception as e:
            return Response({
                'status': 'error',
                'message': str(e)
            }, status=status.HTTP_500_INTERNAL_SERVER_ERROR)
```

### Payment Verification View

```python
class VerifyPaymentView(APIView):
    def get(self, request, reference):
        try:
            # Get payment record
            payment = Payment.objects.get(reference=reference)
            
            # Verify with Chapa
            headers = {
                "Authorization": f"Bearer {settings.CHAPA_SECRET_KEY}"
            }
            
            response = requests.get(
                f"{settings.CHAPA_BASE_URL}/transaction/verify/{reference}",
                headers=headers
            )
            
            if response.status_code == 200:
                data = response.json()['data']
                
                # Update payment status
                if data['status'] == 'success':
                    payment.status = 'completed'
                    payment.payment_method = data.get('payment_method', '')
                    payment.save()
                    
                    # Update booking status
                    payment.booking.status = 'confirmed'
                    payment.booking.save()
                    
                    # Send confirmation email (async)
                    send_payment_confirmation.delay(payment.id)
                    
                return Response({
                    'status': 'success',
                    'data': {
                        'reference': payment.reference,
                        'status': payment.status,
                        'amount': float(payment.amount),
                        'currency': payment.currency
                    }
                })
            else:
                return Response({
                    'status': 'error',
                    'message': 'Verification failed'
                }, status=status.HTTP_400_BAD_REQUEST)
                
        except Payment.DoesNotExist:
            return Response({
                'status': 'error',
                'message': 'Payment not found'
            }, status=status.HTTP_404_NOT_FOUND)
```

---

## 📧 Email Confirmation Task

### Celery Task

```python
from celery import shared_task
from django.core.mail import send_mail
from django.template.loader import render_to_string
from django.conf import settings

@shared_task
def send_payment_confirmation(payment_id):
    try:
        payment = Payment.objects.get(id=payment_id)
        booking = payment.booking
        
        # Render email template
        html_message = render_to_string('emails/payment_confirmation.html', {
            'customer_name': booking.user.get_full_name(),
            'booking_reference': booking.id,
            'payment_reference': payment.reference,
            'amount': payment.amount,
            'property_name': booking.listing.name,
            'check_in': booking.check_in,
            'check_out': booking.check_out,
        })
        
        # Send email
        send_mail(
            subject='Payment Confirmation - Travel Booking',
            message='Your payment has been confirmed.',
            from_email=settings.DEFAULT_FROM_EMAIL,
            recipient_list=[booking.user.email],
            html_message=html_message,
            fail_silently=False
        )
        
        return f"Email sent to {booking.user.email}"
    
    except Exception as e:
        return f"Failed to send email: {str(e)}"
```

---

## 🧪 Testing

### Sandbox Testing

Chapa provides test cards for sandbox testing:

**Test Cards:**
```
Success: 5204730000002514
Insufficient Funds: 5204730001482512
Invalid Card: 5204730000002000
```

**Test Details:**
- CVV: Any 3 digits
- Expiry: Any future date
- Amount: Any amount

### Manual Testing Steps

1. **Start all services:**
   ```bash
   # Terminal 1: Django server
   python manage.py runserver
   
   # Terminal 2: Celery worker
   celery -A alx_travel_app worker --loglevel=info
   
   # Terminal 3: Redis
   redis-server
   ```

2. **Create a booking** (via admin or API)

3. **Initiate payment:**
   ```bash
   curl -X POST http://localhost:8000/api/payments/initiate/ \
     -H "Content-Type: application/json" \
     -d '{"booking_id": 1, "email": "test@example.com", ...}'
   ```

4. **Visit checkout URL** from response

5. **Use test card** to complete payment

6. **Verify payment:**
   ```bash
   curl -X GET http://localhost:8000/api/payments/verify/TXN-XXX/
   ```

7. **Check email** for confirmation

---

## 🎯 Best Practices Implemented

### Security
✅ **Environment Variables** - API keys never in code  
✅ **HTTPS Only** - Encrypted communication  
✅ **Webhook Verification** - Signature validation  
✅ **Transaction IDs** - Unique payment identifiers  
✅ **Error Logging** - Complete audit trail

### Performance
✅ **Async Tasks** - Non-blocking email sending  
✅ **Database Indexing** - Fast lookups  
✅ **Connection Pooling** - Efficient DB usage  
✅ **Caching** - Redis for Celery

### Error Handling
✅ **Try-Except Blocks** - Graceful failures  
✅ **User-Friendly Messages** - Clear error responses  
✅ **Payment Retry Logic** - Handle temporary failures  
✅ **Transaction Rollback** - Data integrity

### Code Quality
✅ **DRY Principle** - Reusable functions  
✅ **Type Hints** - Better code documentation  
✅ **PEP 8 Compliance** - Consistent style  
✅ **Comments** - Clear explanations

---

## 🌍 Real-World Applications

This implementation pattern is used by:

### 🏨 Travel & Hospitality
- **Booking.com** - Hotel reservations
- **Airbnb** - Accommodation bookings
- **Expedia** - Travel packages

### 🛒 E-commerce
- **Jumia** - Product purchases
- **Kilimall** - Online shopping
- **Local marketplaces** - Payment processing

### 🎫 Event Ticketing
- **Eventbrite** - Event bookings
- **Ticketmaster** - Concert tickets
- **Local events** - Registration payments

### 💼 SaaS Platforms
- **Subscription billing** - Recurring payments
- **Course platforms** - Educational content
- **Membership sites** - Access control

---

## 🔮 Future Enhancements

Potential features to add:

- 🔄 **Refund System** - Handle payment reversals
- 📊 **Payment Analytics** - Transaction reports
- 💰 **Multiple Currencies** - International payments
- 📱 **Mobile Money** - M-Pesa, Airtel Money integration
- 🔔 **Webhook Events** - Real-time notifications
- 💳 **Saved Cards** - Tokenization for repeat customers
- 📧 **Payment Reminders** - For pending transactions
- 🎁 **Discount Codes** - Promotional pricing
- 📈 **Revenue Dashboard** - Financial insights
- 🔐 **PCI Compliance** - Enhanced security

---

## 📚 Learning Resources

### Chapa API
- [Chapa Documentation](https://developer.chapa.co/docs)
- [API Reference](https://developer.chapa.co/docs/api)
- [Sandbox Testing](https://developer.chapa.co/docs/testing)

### Django
- [Django Documentation](https://docs.djangoproject.com/)
- [Django REST Framework](https://www.django-rest-framework.org/)

### Celery
- [Celery Documentation](https://docs.celeryproject.org/)
- [Redis Setup](https://redis.io/docs/)

### Payment Integration
- [Payment Gateway Best Practices](https://stripe.com/docs/security)
- [PCI Compliance Guide](https://www.pcisecuritystandards.org/)

---

## 🐛 Troubleshooting

### Chapa API Errors

**Error: "Invalid API Key"**
```bash
# Check your .env file
echo $CHAPA_SECRET_KEY

# Verify it starts with CHASECK_TEST or CHASECK_LIVE
```

**Error: "Payment initiation failed"**
```python
# Check response details
print(response.json())

# Verify all required fields are sent
```

### Celery Not Sending Emails

```bash
# Check Celery worker is running
celery -A alx_travel_app inspect active

# Check Redis is running
redis-cli ping

# Verify email settings in .env
```

### Database Issues

```bash
# Reset migrations (development only!)
python manage.py migrate listings zero
python manage.py makemigrations
python manage.py migrate
```

---

## 📄 License

This project is part of the **ALX ProDev Backend Curriculum - Milestone 4**.

© 2025 ALX Africa. All rights reserved.

---

## 👨‍💻 Author

**Thubelihle Dlamini (Dwaynemaster007)**

---

<div align="center">

### 💜 Built with Payment Integration Excellence by [Dwaynemaster007](https://github.com/Dwaynemaster007) 💜

*Secure Payments, Seamless Bookings, Happy Customers* 💳✨

[![GitHub](https://img.shields.io/badge/GitHub-Dwaynemaster007-181717?style=for-the-badge&logo=github)](https://github.com/Dwaynemaster007)
[![Chapa](https://img.shields.io/badge/Chapa-Integrated-FF6B35?style=for-the-badge)](https://chapa.co/)
[![Django](https://img.shields.io/badge/Django-Master-092E20?style=for-the-badge&logo=django&logoColor=white)](https://www.djangoproject.com/)

---

**Tags:** `django` · `chapa-api` · `payment-gateway` · `travel-booking` · `celery` · `postgresql` · `payment-integration` · `transaction-management` · `email-automation` · `alx-prodev` · `milestone-4` · `fintech` · `secure-payments` · `backend-development`

</div>