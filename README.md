# ✈️ ALX Travel App 0x02: Chapa Payments

Milestone 0x02 of the travel booking API: taking booking payments through the [Chapa](https://chapa.co) payment gateway.

![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)
![Django](https://img.shields.io/badge/Django-092E20?style=flat-square&logo=django&logoColor=white)

## What's in this milestone

Everything from 0x01, plus:

| Method | Endpoint | What it does |
| --- | --- | --- |
| `POST` | `/api/payments/initiate/` with `{"booking": <id>}` | Creates a Chapa transaction for one of your bookings, stores a `Pending` `Payment` and returns `checkout_url` |
| `GET` | `/api/payments/verify/?tx_ref=<reference>` | Asks Chapa for the result and marks the payment `Completed` (and the booking `confirmed`) or `Failed` |

### Payment flow

1. The guest creates a booking, then calls the initiate endpoint.
2. They pay on Chapa's hosted checkout page (`checkout_url`).
3. Chapa sends them back to the verify endpoint (`CHAPA_RETURN_URL`), which updates the payment.

Get a test key from the [Chapa dashboard](https://dashboard.chapa.co) and set `CHAPA_SECRET_KEY` in `.env`. Never commit it.

## Run it

You need Python 3.10+. MySQL is optional: leave `DB_NAME` empty in `.env` to use SQLite.

```bash
git clone https://github.com/Dwaynemaster007/alx_travel_app_0x02.git
cd alx_travel_app_0x02
cd alx_travel_app
python -m venv venv && source venv/bin/activate   # Windows: venv\Scripts\activate
pip install -r requirements.txt   # mysqlclient needs the MySQL client headers; remove it if you only use SQLite
cp .env.example .env
python manage.py migrate
python manage.py seed           # sample data
python manage.py runserver       # http://localhost:8000/swagger/
python manage.py test listings  # run the tests
```

## The travel app series

Each repository holds the project as it was at the end of one milestone:

| Repo | Milestone |
| --- | --- |
| [`alx_travel_app`](https://github.com/Dwaynemaster007/alx_travel_app) | Project setup: Django, DRF, CORS, Swagger, MySQL/SQLite settings |
| [`alx_travel_app_0x00`](https://github.com/Dwaynemaster007/alx_travel_app_0x00) | Models (`Listing`, `Booking`, `Review`), serializers and a `seed` command |
| [`alx_travel_app_0x01`](https://github.com/Dwaynemaster007/alx_travel_app_0x01) | CRUD API with DRF `ViewSet`s and a router |
| **`alx_travel_app_0x02`** | Chapa payment initiation and verification |
| [`alx_travel_app_0x03`](https://github.com/Dwaynemaster007/alx_travel_app_0x03) | Celery + RabbitMQ booking confirmation emails |

## Author

**Thubelihle Dlamini**, [GitHub](https://github.com/Dwaynemaster007) · [LinkedIn](https://linkedin.com/in/thubelihledlamini)
