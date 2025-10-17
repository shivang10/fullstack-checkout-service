# Fullstack Checkout Service

A full-stack checkout service application built with Python FastAPI backend and vanilla JavaScript frontend. This demo application provides basic e-commerce functionality including product catalog, shopping cart, and checkout flow.

## Features

### Backend (FastAPI)
- RESTful API with automatic interactive documentation
- Product catalog management
- Shopping cart functionality
- Order processing and management
- In-memory data storage with demo products
- CORS enabled for frontend integration

### Frontend (HTML/CSS/JavaScript)
- Responsive product catalog grid
- Interactive shopping cart with quantity management
- Checkout form with customer details
- Order confirmation display
- Modern UI with gradient themes
- Mobile-friendly design

## Demo Products

The application comes with 8 pre-loaded demo products:
1. Wireless Headphones - $99.99
2. Smart Watch - $199.99
3. Laptop Stand - $49.99
4. Mechanical Keyboard - $129.99
5. Wireless Mouse - $39.99
6. USB-C Hub - $59.99
7. Phone Case - $19.99
8. Portable Charger - $34.99

## Installation

### Prerequisites
- Python 3.8 or higher
- pip (Python package manager)
- (Optional) New Relic account for monitoring

### Setup

1. Clone the repository:
```bash
git clone https://github.com/shivang10/fullstack-checkout-service.git
cd fullstack-checkout-service
```

2. Install dependencies:
```bash
pip install -r requirements.txt
```

3. (Optional) Configure New Relic monitoring:
```bash
# Copy the example environment file
cp .env.example .env

# Edit .env and add your New Relic license key
# You can get your license key from: https://one.newrelic.com/launcher/api-keys-ui.api-keys-launcher
```

## Running the Application

Start the FastAPI server:
```bash
python main.py
```

Or use uvicorn directly:
```bash
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

### Running with New Relic Monitoring

To enable New Relic monitoring, set the required environment variables:

```bash
# Set environment variables
export NEW_RELIC_LICENSE_KEY="your_license_key_here"
export NEW_RELIC_APP_NAME="fullstack-checkout-service"
export NEW_RELIC_ENVIRONMENT="production"

# Start the application
python main.py
```

Or use a `.env` file with python-dotenv (already included):
```bash
# The application will automatically load from .env file
python main.py
```

The application will be available at:
- **Frontend**: http://localhost:8000/shop
- **API Documentation**: http://localhost:8000/docs
- **Alternative API Docs**: http://localhost:8000/redoc
- **New Relic Dashboard**: https://one.newrelic.com/ (if monitoring is enabled)

## API Endpoints

### Products
- `GET /api/products` - Get all products
- `GET /api/products/{product_id}` - Get a specific product

### Checkout
- `POST /api/checkout` - Process checkout and create an order
  - Request body:
    ```json
    {
      "items": [
        {"product_id": 1, "quantity": 2}
      ],
      "customer_name": "John Doe",
      "customer_email": "john@example.com",
      "shipping_address": "123 Main St, City, Country",
      "payment_method": "credit_card"
    }
    ```

### Orders
- `GET /api/orders` - Get all orders
- `GET /api/orders/{order_id}` - Get a specific order

## Usage

1. **Browse Products**: Visit http://localhost:8000/shop to see the product catalog
2. **Add to Cart**: Click "Add to Cart" on any product
3. **View Cart**: Click the shopping cart icon in the header to view your cart
4. **Manage Quantities**: Use +/- buttons to adjust quantities or remove items
5. **Checkout**: Click "Proceed to Checkout" and fill in your details
6. **Place Order**: Submit the form to complete your purchase
7. **Order Confirmation**: View your order details and confirmation

## Project Structure

```
fullstack-checkout-service/
├── main.py              # FastAPI application entry point
├── models.py            # Pydantic models for data validation
├── demo_data.py         # Demo product catalog
├── requirements.txt     # Python dependencies
├── static/              # Frontend files
│   ├── index.html      # Main HTML page
│   ├── style.css       # Styles
│   └── app.js          # JavaScript logic
└── README.md           # Documentation
```

## New Relic Monitoring

This application includes built-in support for New Relic APM (Application Performance Monitoring).

### Features Monitored
- **Performance Metrics**: Response times, throughput, and error rates
- **Distributed Tracing**: End-to-end transaction tracking
- **Custom Attributes**: Business metrics like order amounts, payment methods, cart size
- **Error Tracking**: Automatic exception capture and reporting
- **Transaction Traces**: Detailed performance breakdowns

### Setup Instructions

1. **Sign up for New Relic**: Visit [newrelic.com](https://newrelic.com/) to create a free account

2. **Get your license key**: 
   - Log in to New Relic
   - Navigate to: Account settings → API keys
   - Copy your license key

3. **Configure environment variables**:
   ```bash
   export NEW_RELIC_LICENSE_KEY="your_license_key_here"
   export NEW_RELIC_APP_NAME="fullstack-checkout-service"
   export NEW_RELIC_ENVIRONMENT="production"
   ```

4. **Start your application**: The New Relic agent will automatically initialize and start reporting data

5. **View your data**: Visit the [New Relic dashboard](https://one.newrelic.com/) to see your application metrics

### Custom Metrics Tracked
- `customer.email`: Customer email for transaction correlation
- `payment.method`: Payment method used (credit_card, paypal, etc.)
- `cart.item_count`: Number of items in the cart
- `order.id`: Unique order identifier
- `order.total_amount`: Total order amount
- `order.status`: Order status (pending, processing, completed, cancelled)

### Configuration
The New Relic agent configuration is stored in `newrelic.ini`. You can customize:
- Transaction thresholds
- Error collection rules
- SQL query obfuscation
- Browser monitoring settings
- And more...

For detailed configuration options, see the [New Relic Python agent documentation](https://docs.newrelic.com/docs/apm/agents/python-agent/).

## Technologies Used

### Backend
- **FastAPI**: Modern, fast web framework for building APIs
- **Pydantic**: Data validation using Python type annotations
- **Uvicorn**: ASGI server for running the application
- **New Relic**: Application performance monitoring and observability

### Frontend
- **HTML5**: Semantic markup
- **CSS3**: Modern styling with flexbox and grid
- **Vanilla JavaScript**: No framework dependencies
- **Fetch API**: For making HTTP requests

## Development

The application uses in-memory storage, so all data is reset when the server restarts. This is intentional for demo purposes.

### API Testing

Use the built-in Swagger UI documentation at http://localhost:8000/docs to test API endpoints interactively.

### Customization

- Modify `demo_data.py` to change or add products
- Update `models.py` to add new fields or validation rules
- Customize the UI by editing files in the `static/` directory

## License

This is a demo application for educational purposes.

## Author

Built with ❤️ using FastAPI