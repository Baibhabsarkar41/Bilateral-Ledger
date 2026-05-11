# 📒 BiLedger — Bilateral Ledger App

> A full-stack debt-tracking and UPI payment settlement app for managing shared expenses between two parties.

BiLedger lets you record credit/debit transactions with another user, track outstanding balances in real time, confirm or reject incoming entries, and settle dues instantly via **Razorpay payments** — all in a clean, modern web interface.

---

## ✨ Features

- 🔐 **JWT Authentication** — Secure register & login flow
- 📊 **Dashboard** — At-a-glance view of all partners and net balances
- 📒 **Khata (Ledger) View** — Full paginated transaction history per partner
- ➕ **Create Transactions** — Log a credit or debit with a description
- ✅ **Confirm / ❌ Reject** — Counterparty must confirm before balances update
- 💸 **Pay Now (Razorpay)** — Settle outstanding dues with a single click; balance auto-resets on successful payment

---

## 🏗️ Tech Stack

| Layer | Technology |
|---|---|
| **Frontend** | React 18, React Router v6, Axios, react-hot-toast |
| **Backend** | Python 3, Flask 3, Flask-JWT-Extended, Flask-SQLAlchemy |
| **Database** | SQLite (dev) / any SQLAlchemy-compatible DB |
| **Payments** | Razorpay SDK (order creation + HMAC-SHA256 signature verification) |

---

## 📁 Project Structure

```
Bilateral-Ledger/
├── backend/
│   ├── app.py              # Flask application factory
│   ├── database.py         # SQLAlchemy db instance
│   ├── requirements.txt
│   ├── Dockerfile
│   ├── .env.example        # ← copy to .env and fill in secrets
│   ├── models/
│   │   ├── user.py
│   │   └── transaction.py
│   └── routes/
│       ├── auth.py         # /api/auth/*
│       ├── transactions.py # /api/transactions/*
│       ├── users.py        # /api/users/*
│       └── payments.py     # /api/payments/*
├── frontend/
│   ├── public/
│   │   └── index.html
│   ├── src/
│   │   ├── App.js
│   │   ├── components/     # auth, dashboard, ledger, shared
│   │   ├── context/
│   │   ├── hooks/
│   │   ├── styles/
│   │   └── utils/
│   ├── package.json
│   ├── Dockerfile
│   └── nginx.conf
└── docker-compose.yml
```

---

## 🚀 Getting Started

### Prerequisites

- **Node.js** 18+ and npm
- **Python** 3.10+
- A [Razorpay](https://razorpay.com/) account (for payment features)

---

### 1. Clone the repo

```bash
git clone https://github.com/<your-username>/Bilateral-Ledger.git
cd Bilateral-Ledger
```

### 2. Backend setup

```bash
cd backend

# Create and activate a virtual environment
python -m venv venv
# Windows:
venv\Scripts\activate
# macOS/Linux:
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt

# Configure environment variables
copy .env.example .env   # Windows
# cp .env.example .env   # macOS/Linux
# Edit .env and fill in your secrets (see Environment Variables below)

# Run the development server
python app.py
# Backend runs at http://localhost:5000
```

### 3. Frontend setup

```bash
cd frontend

# Install dependencies
npm install

# Configure environment variables
# Create a .env file with:
# REACT_APP_API_URL=http://localhost:5000
# DANGEROUSLY_DISABLE_HOST_CHECK=true  (dev only)

# Start the development server
npm start
# Frontend runs at http://localhost:3000
```

---

## 🐳 Docker (Recommended for Production)

```bash
# From the project root
docker-compose up --build
```

| Service | URL |
|---|---|
| Frontend | http://localhost:3000 |
| Backend API | http://localhost:5000 |

> ⚠️ Update the `SECRET_KEY` and `JWT_SECRET_KEY` values in `docker-compose.yml` before deploying to production.

---

## 🔑 Environment Variables

### Backend (`backend/.env`)

| Variable | Description | Example |
|---|---|---|
| `SECRET_KEY` | Flask session secret | `my-super-secret-key` |
| `JWT_SECRET_KEY` | JWT signing secret | `my-jwt-secret` |
| `DATABASE_URL` | SQLAlchemy connection string | `sqlite:///ledger.db` |
| `FLASK_ENV` | Flask environment | `development` |
| `FLASK_DEBUG` | Enable debug mode | `1` |
| `RAZORPAY_KEY_ID` | Razorpay API key ID | `rzp_test_XXXXXXXX` |
| `RAZORPAY_KEY_SECRET` | Razorpay API secret | `your-secret` |

> **Never commit your `.env` file.** It is already listed in `.gitignore`.

---

## 📡 API Reference

All API routes are prefixed with `/api`.

### Auth — `/api/auth`

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/register` | Create a new user account |
| `POST` | `/login` | Log in and receive a JWT token |

### Transactions — `/api/transactions`

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/` | Create a new transaction (PENDING) |
| `GET` | `/` | List your transactions (filterable by status, partner) |
| `GET` | `/<id>` | Get a single transaction |
| `POST` | `/<id>/confirm` | Counterparty confirms a pending transaction |
| `POST` | `/<id>/reject` | Reject a pending transaction |
| `GET` | `/balance/<partner_id>` | Get net balance with a partner |
| `GET` | `/partners` | List all partners with balances |

### Payments — `/api/payments`

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/create-order` | Create a Razorpay order for outstanding dues |
| `POST` | `/verify` | Verify payment signature and auto-settle the balance |

---

## 💡 How the Ledger Logic Works

1. **User A** creates a transaction (e.g., "I paid ₹500 for User B" → `credit` type).
2. The transaction is `PENDING` until **User B** confirms it.
3. Once **confirmed**, the balance updates: User B owes User A ₹500.
4. **User B** can click **Pay Now** on the dashboard → a Razorpay order is created for the exact outstanding amount.
5. After successful UPI payment, the backend verifies the HMAC-SHA256 signature and records an auto-confirmed settlement transaction → balance resets to ₹0.

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature`
3. Commit your changes: `git commit -m "feat: add your feature"`
4. Push to the branch: `git push origin feature/your-feature`
5. Open a Pull Request

---

## ⭐ If you find this useful...

Give it a ⭐ on GitHub — it helps others discover the project!
