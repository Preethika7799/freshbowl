```markdown
# 🍲 FreshBowl - Food Ordering & E-Commerce Platform

## Overview
FreshBowl is a Django-based e-commerce platform for food ordering and delivery with secure payments, inventory management, and order tracking.

## ✨ Features
- 🛒 Product catalog with categories
- 🛍️ Shopping cart management
- 💳 Stripe payment integration
- 📦 Order tracking & management
- 🎟️ Coupon system
- 🔐 User authentication
- 📍 Multiple shipping addresses

## Tech Stack
- **Backend:** Django 2.2+, Python
- **Database:** SQLite/PostgreSQL
- **Frontend:** Bootstrap, HTML5, CSS3, JavaScript
- **Payment:** Stripe API
- **Deployment:** Docker & Docker Compose

## 🚀 Installation

### Local Setup
```bash
git clone https://github.com/Preethika7799/freshbowl.git
cd freshbowl
python -m venv env
source env/bin/activate
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver
```

Visit: `http://localhost:8000`

### Docker Setup
```bash
docker-compose up --build
```

## 💻 Code Examples

### Add to Cart
```python
def add_to_cart(request, slug):
    item = get_object_or_404(Item, slug=slug)
    order_item, created = OrderItem.objects.get_or_create(
        item=item, user=request.user, ordered=False
    )
    order = Order.objects.get_or_create(user=request.user, ordered=False)[0]
    order.items.add(order_item)
    return redirect("core:order-summary")
```

### Order Model
```python
class Order(models.Model):
    user = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    items = models.ManyToManyField(OrderItem)
    ordered = models.BooleanField(default=False)
    coupon = models.ForeignKey('Coupon', on_delete=models.SET_NULL, null=True)
    
    def get_total(self):
        total = sum(item.get_final_price() for item in self.items.all())
        if self.coupon:
            total -= self.coupon.amount
        return total
```

### Payment Processing
```python
payment = Payment.objects.create(
    stripe_charge_id=str(uuid.uuid4()),
    user=request.user,
    amount=order.get_total()
)
order.ordered = True
order.payment = payment
order.save()
```

## 📊 Sample Output
```json
{
  "status": "success",
  "order_id": "ORDER-12345",
  "total": 45.99,
  "coupon_applied": "SAVE20",
  "discount": 10.00,
  "final_total": 35.99
}
```

## 📁 Project Structure
```
freshbowl/
├── core/
│   ├── models.py
│   ├── views.py
│   ├── forms.py
│   └── templates/
├── templates/
├── static/
├── manage.py
├── requirements.txt
├── Dockerfile
├── docker-compose.yml
└── README.md
```
