# ✈️ ALX Travel App 0x02: Chapa Payments

Milestone 0x02 of the travel booking API: taking booking payments through the [Chapa](https://chapa.co) payment gateway.

![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)
![Django](https://img.shields.io/badge/Django-092E20?style=flat-square&logo=django&logoColor=white)

## What's in this milestone

| File | Purpose |
| --- | --- |
| [`listings/models.py`](alx_travel_app/listings/models.py) | `Payment` model: booking reference, amount, transaction ID and status |
| [`listings/views.py`](alx_travel_app/listings/views.py) | `InitiatePaymentView` creates a Chapa transaction and returns the checkout URL; `VerifyPaymentView` confirms it and updates the payment status |

### Payment flow

1. The client sends `POST` with booking details to the initiate endpoint.
2. The API calls Chapa's `/transaction/initialize` and stores a `Pending` payment.
3. The user pays on Chapa's hosted checkout page.
4. Chapa redirects to the verify endpoint, which marks the payment `Completed` or `Failed`.

Set `CHAPA_SECRET_KEY` as an environment variable. Never commit it.

## The travel app series

Each repository holds the code added in one milestone of the **ALX ProDev Backend** travel booking project:

| Repo | Milestone |
| --- | --- |
| [`alx_travel_app`](https://github.com/Dwaynemaster007/alx_travel_app) | Project setup: Django, DRF, Swagger, MySQL configuration |
| [`alx_travel_app_0x00`](https://github.com/Dwaynemaster007/alx_travel_app_0x00) | Models (`Listing`, `Booking`, `Review`), serializers and a seed command |
| [`alx_travel_app_0x01`](https://github.com/Dwaynemaster007/alx_travel_app_0x01) | CRUD API with DRF `ViewSet`s and routers |
| [`alx_travel_app_0x02`](https://github.com/Dwaynemaster007/alx_travel_app_0x02) | Chapa payment initiation and verification |
| [`alx_travel_app_0x03`](https://github.com/Dwaynemaster007/alx_travel_app_0x03) | Celery + RabbitMQ background email tasks |

## Author

**Thubelihle Dlamini**, [GitHub](https://github.com/Dwaynemaster007) · [LinkedIn](https://linkedin.com/in/thubelihledlamini)
