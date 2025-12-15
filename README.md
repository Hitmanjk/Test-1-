# Test-1-<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Rega Finds - Clothing Wearables</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background: linear-gradient(to bottom, #8B4513 0%, #006400 50%, #FFFACD 100%); /* Brown, dark green, light cream */
            color: #333;
        }
        header {
            background-color: #8B4513;
            color: white;
            padding: 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        header h1 {
            margin: 0;
        }
        #view-cart-btn {
            background-color: #006400;
            color: white;
            border: none;
            padding: 10px 15px;
            border-radius: 5px;
            cursor: pointer;
        }
        #view-cart-btn:hover {
            background-color: #004d00;
        }
        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
        }
        .product-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 20px;
        }
        .product {
            background: white;
            border-radius: 8px;
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
            padding: 20px;
            text-align: center;
            transition: transform 0.3s;
        }
        .product:hover {
            transform: scale(1.05);
        }
        .product img {
            width: 100%;
            height: 200px;
            object-fit: cover;
            border-radius: 8px;
            cursor: pointer;
        }
        .product.selected {
            border: 3px solid #006400;
        }
        .price-input {
            width: 100px;
            margin: 10px 0;
        }
        button {
            background-color: #006400;
            color: white;
            border: none;
            padding: 10px;
            border-radius: 5px;
            cursor: pointer;
            margin: 5px;
        }
        button:hover {
            background-color: #004d00;
        }
        /* Cart Modal */
        .modal {
            display: none;
            position: fixed;
            z-index: 1;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            overflow: auto;
            background-color: rgba(0,0,0,0.4);
        }
        .modal-content {
            background-color: white;
            margin: 15% auto;
            padding: 20px;
            border: 1px solid #888;
            width: 80%;
            max-width: 600px;
            border-radius: 8px;
        }
        .close {
            color: #aaa;
            float: right;
            font-size: 28px;
            font-weight: bold;
            cursor: pointer;
        }
        .close:hover {
            color: black;
        }
        #cart-items {
            list-style: none;
            padding: 0;
        }
        #cart-items li {
            margin-bottom: 10px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        .quantity-input {
            width: 50px;
        }
        footer {
            text-align: center;
            padding: 20px;
            background-color: #8B4513;
            color: white;
        }
    </style>
</head>
<body>
    <header>
        <h1>Rega Finds</h1>
        <button id="view-cart-btn">View Cart</button>
    </header>

    <div class="container">
        <h2>Our Collection</h2>
        <div class="product-grid" id="products">
            <!-- Product 1 -->
            <div class="product" data-id="1">
                <img src="https://images.unsplash.com/photo-1521572163474-6864f9cf17ab?w=300" alt="T-Shirt" onclick="selectImage(this)">
                <h3>T-Shirt</h3>
                <p>Price: ₹<input type="number" class="price-input" value="500" onchange="updatePrice(this)"></p>
                <button onclick="editImage(this)">Edit Image</button>
                <button onclick="addToCart(1, 'T-Shirt', this.previousElementSibling.previousElementSibling.value)">Add to Cart</button>
            </div>
            <!-- Product 2 -->
            <div class="product" data-id="2">
                <img src="https://images.unsplash.com/photo-1544441893-675973e31985?w=300" alt="Jeans" onclick="selectImage(this)">
                <h3>Jeans</h3>
                <p>Price: ₹<input type="number" class="price-input" value="1200" onchange="updatePrice(this)"></p>
                <button onclick="editImage(this)">Edit Image</button>
                <button onclick="addToCart(2, 'Jeans', this.previousElementSibling.previousElementSibling.value)">Add to Cart</button>
            </div>
            <!-- Product 3 -->
            <div class="product" data-id="3">
                <img src="https://images.unsplash.com/photo-1591047139829-d91aecb6caea?w=300" alt="Jacket" onclick="selectImage(this)">
                <h3>Jacket</h3>
                <p>Price: ₹<input type="number" class="price-input" value="2500" onchange="updatePrice(this)"></p>
                <button onclick="editImage(this)">Edit Image</button>
                <button onclick="addToCart(3, 'Jacket', this.previousElementSibling.previousElementSibling.value)">Add to Cart</button>
            </div>
            <!-- Add more products as needed -->
        </div>
    </div>

    <!-- Cart Modal -->
    <div id="cart-modal" class="modal">
        <div class="modal-content">
            <span class="close" onclick="closeCart()">&times;</span>
            <h3>Your Cart</h3>
            <ul id="cart-items"></ul>
            <p>Total: ₹<span id="total">0</span></p>
            <h4>Payment Options</h4>
            <form id="payment-form">
                <label><input type="radio" name="payment" value="cod" checked> Cash on Delivery (COD)</label><br>
                <label><input type="radio" name="payment" value="card"> Card Payment</label><br>
                <label><input type="radio" name="payment" value="upi"> UPI Transaction</label><br>
                <button type="button" onclick="processPayment()">Proceed to Payment</button>
            </form>
        </div>
    </div>

    <footer>
        <p>&copy; 2023 Rega Finds. All rights reserved.</p>
    </footer>

    <script src="https://checkout.razorpay.com/v1/checkout.js"></script>
    <script>
        let cart = JSON.parse(localStorage.getItem('cart')) || [];
        let total = 0;

        function updateCartDisplay() {
            const cartItems = document.getElementById('cart-items');
            cartItems.innerHTML = '';
            total = 0;
            cart.forEach((item, index) => {
                const li = document.createElement('li');
                li.innerHTML = `
                    ${item.name} - ₹${item.price} x 
                    <input type="number" class="quantity-input" value="${item.quantity || 1}" min="1" onchange="updateQuantity(${index}, this.value)">
                    = ₹${(item.price * (item.quantity || 1))}
                    <button onclick="removeFromCart(${index})">Remove</button>
                `;
                cartItems.appendChild(li);
                total += item.price * (item.quantity || 1);
            });
            document.getElementById('total').textContent = total;
        }

        function addToCart(id, name, price) {
            const existing = cart.find(item => item.id === id);
            if (existing) {
                existing.quantity = (existing.quantity || 1) + 1;
            } else {
                cart.push({ id, name, price: parseInt(price), quantity: 1 });
            }
            localStorage.setItem('cart', JSON.stringify(cart));
            alert('Added to cart!');
        }

        function selectImage(img) {
            img.parentElement.classList.toggle('selected');
        }

        function editImage(button) {
            const input = document.createElement('input');
            input.type = 'file';
            input.accept = 'image/*';
            input.onchange = function(e) {
                const file = e.target.files[0];
                if (file) {
                    const reader = new FileReader();
                    reader.onload = function(event) {
                        button.previousElementSibling.previousElementSibling.src = event.target.result;
                    };
                    reader.readAsDataURL(file);
                }
            };
            input.click();
        }

        function updatePrice(input) {
            // Price updated dynamically
        }

        function updateQuantity(index, qty) {
            cart[index].quantity = parseInt(qty);
            localStorage.setItem('cart', JSON.stringify(cart));
            updateCartDisplay();
        }

        function removeFromCart(index) {
            cart.splice(index, 1);
            localStorage.setItem('cart', JSON.stringify(cart));
            updateCartDisplay();
        }

        function openCart() {
            updateCartDisplay();
            document.getElementById('cart-modal').style.display = 'block';
        }

        function closeCart() {
            document.getElementById('cart-modal').style.display = 'none';
        }

        function processPayment() {
            const paymentMethod = document.querySelector('input[name="payment"]:checked').value;
            if (cart.length === 0) {
                alert('Cart is empty!');
                return;
            }
            if (paymentMethod === 'cod') {
                alert('Order placed successfully! Cash on Delivery selected.');
                cart = [];
                localStorage.setItem('cart', JSON.stringify(cart));
                closeCart();
            } else {
                // Razorpay for card or UPI
                const options = {
                    key: 'YOUR_RAZORPAY_KEY_ID', // Get from Razorpay dashboard (test key)
                    amount: total * 100, // Amount in paisa
                    currency: 'INR',
                    name: 'Rega Finds',
                    description: 'Purchase',
                    method: paymentMethod === 'upi' ? 'upi' : 'card', // Prefill method
                    handler: function(response) {
                        alert('Payment successful! ID: ' + response.razorpay_payment_id);
                        cart = [];
                        localStorage.setItem('cart', JSON.stringify(cart));
                        closeCart();
                    }
                };
                const rzp = new Razorpay(options);
                rzp.open();
            }
        }

        document.getElementById('view-cart-btn').onclick = openCart;
    </script>
</body>
</html>
