# BizNova (Smart Kirana) – AI-Integrated Retail Platform

[![Live Demo](https://img.shields.io/badge/Live_Demo-Vercel-black?logo=vercel)](https://smart-kirana-beta.vercel.app)
![React](https://img.shields.io/badge/React-18-61DAFB?logo=react&logoColor=white)
![Node.js](https://img.shields.io/badge/Node.js-Express-339933?logo=nodedotjs&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-Atlas-47A248?logo=mongodb&logoColor=white)
![Gemini](https://img.shields.io/badge/Gemini-2.5_Flash-8E75B2?logo=googlegemini&logoColor=white)

A full-stack MERN application designed to help small kirana (grocery) shop owners manage their businesses efficiently. The platform provides inventory management, sales and expense tracking, profit analytics, and an AI-powered business assistant that answers queries using the shop's actual business data.

- **Live Demo:** https://smart-kirana-beta.vercel.app
- **Backend API:** https://smart-kirana-backend-7qlq.onrender.com/health

> **Note:** The backend is hosted on Render's free tier. The first request after a period of inactivity may take 30–60 seconds while the server wakes up.

---

## Features

### For Retailers (Shop Owners)

- **Dashboard** – View daily sales, profits, low-stock alerts, and business performance charts.
- **Inventory, Sales & Expenses** – Manage products, track transactions, and automatically deduct stock whenever a sale is recorded.
- **Profit Analytics** – Analyze daily trends, top-selling products, category-wise sales, and month-over-month performance.
- **AI Business Assistant** – Ask questions such as "What were my sales today?" or "Which items are running low?" and receive answers based on actual business data.
- **AI Bill Scanner** – Upload purchase bill images and use Gemini Vision to extract product details and add them to inventory.
- **Voice & Text Commands** – Perform operations using natural language, such as "Add 10 kg rice" or "Sold 2 milk packets", in English, Hindi, or Telugu. All operations require user confirmation before database updates.
- **Business Health Score (0–100)** – Evaluate business performance based on profit margins, stock health, sales consistency, and inventory efficiency, with actionable improvement suggestions.
- **Festival Demand Alerts** – Identify upcoming festivals and recommend products to restock using historical sales and current inventory data.
- **Smart Discounts** – Recommend discounts for products nearing expiry or selling slowly and publish them as Hot Deals.

### For Customers

- Discover nearby shops based on location.
- Browse available products and deals.
- Place orders through a shop's AI-powered chat assistant.

### For Wholesalers

- List products and receive orders from retailers.
- Manage orders through the stages: Requested → Accepted → Packed → Dispatched → Delivered.
- Track order and payment statuses.

---

## Tech Stack

| Layer | Technologies |
| --- | --- |
| Frontend | React 18, Tailwind CSS, Recharts, i18next (English / Hindi / Telugu), ZXing Barcode Scanning |
| Backend | Node.js, Express.js, JWT, bcrypt, express-rate-limit, express-validator, Multer, Nodemailer |
| Database | MongoDB Atlas, Mongoose (13 collections, compound and geospatial indexes) |
| AI Integration | Google Gemini 2.5 Flash (assistant, insights, vision); OpenAI GPT-4o-mini (conversational commands) |
| Deployment | Vercel (Frontend), Render (Backend), MongoDB Atlas (Database) |

---

## System Architecture

```text
                  React Frontend (Vercel)
                           |
                      HTTPS + JWT
                           |
                           v
                   Express API (Render)
                           |
                +----------+----------+
                |                     |
                v                     v
           MongoDB Atlas         Gemini / OpenAI
             Database              APIs
```

### How It Works

- **Authentication:** Users log in using their phone number and password. The backend generates a JWT, and authentication middleware verifies user roles (retailer, customer, or wholesaler) before allowing access to protected routes. Login and password-reset endpoints are rate-limited.

- **AI Business Assistant:** When a retailer submits a query, the backend retrieves inventory, sales, expenses, business health scores, and festival forecasts in parallel using `Promise.all`. This information is used to construct a contextual prompt that is sent to Gemini.

- **Safe AI Operations:** For commands that modify business data, the AI first generates a structured action. The application displays a confirmation message (for example, "Add 10 kg rice?"). Database modifications occur only after the user confirms the action.

- **Analytics & Reports:** Sales and profit reports are generated using MongoDB aggregation pipelines, supported by compound indexes such as `{ user_id: 1, date: -1 }` for efficient querying.

---

## Getting Started

### Prerequisites

- Node.js 18 or later
- MongoDB (Local installation or MongoDB Atlas)
- Google Gemini API key

### Installation

Clone the repository:

```bash
git clone https://github.com/psp2535/Smart-Kirana.git
cd Smart-Kirana
npm run install-all
```

### Environment Variables

Create the environment files using the provided examples:

```bash
cp backend/.env.example backend/.env
cp frontend/.env.example frontend/.env
```

**Backend – `backend/.env`**

| Variable | Description |
| --- | --- |
| `MONGODB_URI` | MongoDB connection string |
| `JWT_SECRET` | A long, secure random string |
| `GEMINI_API_KEY` | Google Gemini API key |
| `FRONTEND_URL` | Frontend URL allowed by CORS (e.g., `http://localhost:3000`) |

Optional variables:

- `OPENAI_API_KEY` – Enables conversational commands.
- `EMAIL_*` – Configures password-reset emails.

Refer to `backend/.env.example` for the complete list of supported variables.

**Frontend – `frontend/.env`**

| Variable | Description |
| --- | --- |
| `REACT_APP_API_URL` | Backend API base URL without `/api` (e.g., `http://localhost:5000`) |

### Run Locally

Start the application:

```bash
npm run dev
```

The application will be available at:

- **Frontend:** http://localhost:3000
- **Backend:** http://localhost:5000

---

## Key API Endpoints

| Method | Endpoint | Description |
| --- | --- | --- |
| POST | `/api/auth/register`, `/api/auth/login` | Retailer registration and login |
| GET / POST | `/api/inventory` | Retrieve and add inventory products |
| POST | `/api/inventory/parse-bill` | Extract product details from bill images using Gemini Vision |
| GET / POST | `/api/sales` | Retrieve and record sales with automatic stock updates |
| GET | `/api/profit-analytics/top-products` | Retrieve best-selling products |
| GET | `/api/profit-analytics/time-series` | Analyze sales and expenses over time |
| POST | `/api/chatbot/chat` | Interact with the retailer AI assistant |
| POST | `/api/conversational/parse` → `/execute` | Process and execute confirmed voice/text commands |
| GET | `/api/campaigns/recommendations` | Retrieve smart discount recommendations |
| GET | `/api/wholesalers/nearby` | Find nearby wholesalers using MongoDB geospatial queries |
| POST | `/api/wholesalers/orders` | Place orders with wholesalers |
| GET | `/health` | Check backend server health |

---

## Project Structure

```text
Smart-Kirana/
│
├── backend/
│   ├── src/
│   │   ├── config/          # MongoDB configuration
│   │   ├── controllers/     # Request handlers (sales, inventory, AI, bill scanning, etc.)
│   │   ├── middleware/      # JWT authentication, rate limiting, validation
│   │   ├── models/          # Mongoose schemas
│   │   ├── routes/          # API routes
│   │   ├── services/        # Gemini, health score, festival forecast, discounts
│   │   └── server.js        # Backend entry point
│   │
│   └── smartkirana_festival_dataset_150.csv
│
├── frontend/
│   ├── public/locales/      # English, Hindi, and Telugu translations
│   └── src/
│       ├── components/      # Chatbots, charts, POS, notifications
│       ├── pages/           # Retailer, customer, and wholesaler dashboards
│       ├── services/        # API client
│       └── contexts/        # Authentication and theme state
│
├── render.yaml              # Render backend configuration
└── DEPLOYMENT.md            # Deployment instructions
```

---

## Deployment

The application is deployed using the following services:

- **Frontend – Vercel:** Deploy the frontend with the Root Directory configured as `frontend`. Set `REACT_APP_API_URL` to the deployed Render backend URL.
- **Backend – Render:** Deploy the backend using the provided `render.yaml` configuration. Configure the required environment variables in the Render dashboard.
- **Database – MongoDB Atlas:** Configure the MongoDB connection string and network access. The current deployment allows access from `0.0.0.0/0` because Render's outbound IP addresses may change.

After deploying the frontend, update `FRONTEND_URL` in the Render dashboard with the deployed Vercel URL to ensure the backend CORS configuration allows frontend requests.

For detailed deployment instructions, refer to [DEPLOYMENT.md](./DEPLOYMENT.md).

---

## Team

Built collaboratively by a team of three:

| Name | GitHub | LinkedIn |
| --- | --- | --- |
| Pathange Sai Pranav | [@psp2535](https://github.com/psp2535) | [LinkedIn](https://www.linkedin.com/in/pathangesaipranav/) |
| Surendra Chakali | [@suri5790](https://github.com/suri5790) | [LinkedIn](https://www.linkedin.com/in/surendrachakali/) |
| Duppala Satwik | [@Satwik2529](https://github.com/Satwik2529) | [LinkedIn](https://www.linkedin.com/in/satwik2529dsk/) |
| Gedela Sasidhar Chakrapani | [@Sasidhar1906](https://github.com/Sasidhar1906) | [LinkedIn](https://www.linkedin.com/in/sasidhar1906/) |

---

## License

This project is licensed under the **MIT License**.
