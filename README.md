# SwasthiQ Pharmacy Management System

**Full-stack pharmacy inventory management dashboard with real-time sync and business intelligence.**

## 🏥 Overview

SwasthiQ is a production-ready pharmacy management system that streamlines inventory tracking, sales management, and business analytics. Built with modern web technologies, it provides real-time data synchronization and smart inventory status management.

---

## ✨ Key Features

- **📦 Inventory Management** — Complete CRUD operations with auto-status calculation
- **💰 Sales Tracking** — Record and analyze daily sales transactions
- **📊 Dashboard Analytics** — Real-time business intelligence and KPIs
- **🔍 Advanced Search & Filtering** — Find medicines by name, category, status
- **📈 Business Reports** — Sales summaries, purchase order tracking
- **🔐 Role-Based Access** — Secure authentication and authorization
- **🔄 Real-Time Sync** — Instant data updates across all users
- **📱 Responsive Design** — Works on desktop, tablet, and mobile

---

## 🛠️ Tech Stack

| Layer | Technology | Purpose |
|-------|-----------|---------|
| **Frontend** | React 19 + Vite | Modern, fast UI with HMR |
| **Styling** | TailwindCSS | Utility-first responsive design |
| **HTTP Client** | Axios | API requests with interceptors |
| **Backend** | FastAPI | Fast Python web framework |
| **Database** | SQLite / PostgreSQL | Reliable data storage |
| **ORM** | SQLAlchemy | Database abstraction layer |
| **Server** | Uvicorn | ASGI server for FastAPI |
| **Deployment** | Docker | Container-based deployment |

---

## 📁 Project Structure

```
pharmacy-Management-system/
├── backend/
│   ├── main.py                 # FastAPI app entry point
│   ├── database.py             # Database configuration
│   ├── models.py               # SQLAlchemy models (Medicine, Sale)
│   ├── schemas.py              # Pydantic request/response schemas
│   ├── routers/
│   │   ├── dashboard.py        # Dashboard endpoints
│   │   └── inventory.py        # Inventory CRUD endpoints
│   ├── seed.py                 # Database seeding with sample data
│   ├── requirements.txt        # Python dependencies
│   ├── Dockerfile              # Container configuration
│   └── pharmacy.db             # SQLite database file
│
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── Dashboard.jsx   # Main dashboard view
│   │   │   ├── InventoryTable.jsx
│   │   │   ├── SearchBar.jsx
│   │   │   └── AddMedicineForm.jsx
│   │   ├── pages/
│   │   │   ├── Home.jsx
│   │   │   ├── Inventory.jsx
│   │   │   ├── Sales.jsx
│   │   │   └── Analytics.jsx
│   │   ├── App.jsx
│   │   └── main.jsx
│   ├── public/
│   ├── Dockerfile              # Frontend container
│   ├── nginx.conf              # Nginx configuration
│   ├── vite.config.js
│   ├── package.json
│   └── .env.example            # Environment variables template
│
├── docker-compose.yml          # Local development orchestration
├── nginx.prod.conf             # Production nginx config
├── README.md
└── DEPLOYMENT.md
```

---

## 🚀 Getting Started

### Prerequisites

- Python 3.10+
- Node.js 18+
- Docker & Docker Compose (optional)

### Backend Setup

```bash
# Navigate to backend
cd backend

# Create virtual environment
python -m venv venv

# Activate virtual environment
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Run database seeding
python seed.py

# Start server
uvicorn main:app --reload
```

Backend runs at: `http://localhost:8000`

API Docs: `http://localhost:8000/docs`

### Frontend Setup

```bash
# Navigate to frontend
cd frontend

# Install dependencies
npm install

# Start development server
npm run dev
```

Frontend runs at: `http://localhost:5173`

### Using Docker Compose

```bash
# Build and start all services
docker-compose up -d

# View logs
docker-compose logs -f backend

# Stop services
docker-compose down
```

---

## 📡 API Endpoints

### Dashboard Endpoints

```
GET /api/dashboard/summary
├─ Returns: Daily sales, items sold, low stock count, sales change %
├─ Example Response:
└─ {
     "today_sales": 2920.0,
     "items_sold": 15,
     "low_stock_count": 2,
     "sales_change_pct": 12.5
   }

GET /api/dashboard/purchase-orders
├─ Returns: Purchase order summary and status

GET /api/dashboard/recent-sales
└─ Returns: Last 10 sale records with details
```

### Inventory Endpoints

```
GET /api/inventory/
├─ Query Params: ?search=paracetamol&status=Active&category=Analgesic
├─ Returns: List of medicines with filters applied

GET /api/inventory/overview
├─ Returns: Total count, active count, total stock value

POST /api/inventory/
├─ Body: { "name", "generic_name", "category", "quantity", "mrp", ... }
├─ Returns: Created medicine record

PUT /api/inventory/{id}
├─ Body: Complete medicine object
├─ Returns: Updated medicine record

PATCH /api/inventory/{id}/status
├─ Body: { "status": "Active" }
└─ Returns: Updated medicine record
```

---

## 🗄️ Database Schema

### Medicine Table

```python
class Medicine(Base):
    __tablename__ = "medicines"
    
    id: int (Primary Key)
    name: str (Unique)
    generic_name: str
    category: str
    batch_no: str
    expiry_date: date
    quantity: int (auto-managed)
    cost_price: float
    mrp: float (Maximum Retail Price)
    supplier: str
    status: enum (Active, Low Stock, Expired, Out of Stock)
    created_at: datetime
    updated_at: datetime
```

### Sale Table

```python
class Sale(Base):
    __tablename__ = "sales"
    
    id: int (Primary Key)
    invoice_no: str
    patient_name: str
    amount: float
    items: int (quantity)
    payment_mode: str (Cash, Card, UPI)
    date: date
    status: str (Completed, Pending)
```

---

## 🧠 Smart Status Management

The system automatically calculates medicine status based on:

```python
if quantity == 0:
    status = "Out of Stock"
elif expiry_date < today:
    status = "Expired"
elif quantity < 10:
    status = "Low Stock"
else:
    status = "Active"
```

**This logic runs automatically on every create/update**, preventing data inconsistencies.

---

## 📊 Sample API Requests

### Create Medicine

```bash
curl -X POST http://localhost:8000/api/inventory/ \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Paracetamol 500mg",
    "generic_name": "Acetaminophen",
    "category": "Analgesic",
    "batch_no": "BT-2025-001",
    "expiry_date": "2027-06-15",
    "quantity": 250,
    "cost_price": 1.2,
    "mrp": 2.5,
    "supplier": "PharmaCorp India"
  }'
```

### Search Medicines

```bash
curl "http://localhost:8000/api/inventory/?search=paracetamol&status=Active"
```

### Get Dashboard Summary

```bash
curl http://localhost:8000/api/dashboard/summary
```

---

## 🐳 Docker Deployment

### Build Images

```bash
docker-compose build
```

### Run Services

```bash
docker-compose up -d
```

### Access Services

- Frontend: `http://localhost:3000`
- Backend: `http://localhost:8000`
- MongoDB: `localhost:27017`

---

## 🚀 Production Deployment

### Backend (Render.com)

1. Create new Web Service on Render
2. Connect GitHub repository
3. Set build command: `pip install -r requirements.txt`
4. Set start command: `uvicorn main:app --host 0.0.0.0 --port $PORT`
5. Add environment variables (DATABASE_URL, etc.)
6. Deploy

### Frontend (Vercel)

1. Import repository
2. Set root directory: `frontend`
3. Build command: `npm run build`
4. Add environment variable: `VITE_API_URL=<backend-url>`
5. Deploy

---

## 🔐 Security Features

- ✅ Input validation on all endpoints
- ✅ Error handling with meaningful messages
- ✅ Database transactions for data consistency
- ✅ CORS properly configured
- ✅ Environment variables for sensitive data
- ✅ SQL injection prevention (SQLAlchemy ORM)

---

## 🧪 Testing

```bash
# Run Python tests
pytest backend/tests/

# Run frontend tests
npm test
```

---

## 📈 Performance Optimizations

- ✅ Database indexes on frequently queried fields
- ✅ Lean API responses with `.select()` queries
- ✅ Frontend code splitting with React.lazy
- ✅ Static asset caching with nginx

---

## 🚧 Future Improvements

- [ ] Add authentication/authorization
- [ ] Implement billing and invoicing
- [ ] Add expense tracking
- [ ] Create supplier management module
- [ ] Add barcode scanning
- [ ] Implement automated alerts for low stock
- [ ] Add multi-location support
- [ ] Create mobile app

---

## 📝 Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License — see LICENSE file for details.

---

## 👨‍💼 Author

**Mohd Amaan Khan**
- GitHub: [@amaan921](https://github.com/amaan921)
- LinkedIn: [Profile](https://linkedin.com/in/mohd-amaan-khan-dev)
- Email: amaan2982@gmail.com

---

## ⭐ Show Your Support

If this project helped you, please give it a star! It helps other developers discover it.

