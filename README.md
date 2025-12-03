<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simple Colorful Gadget Store</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700&display=swap" rel="stylesheet">
    
    <!-- Simple, Beginner-Friendly Internal CSS -->
    <style>
        body {
            font-family: 'Inter', sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f0f8ff; /* Light Blue Background */
            color: #333;
        }
        .container {
            max-width: 900px;
            margin: 20px auto;
            padding: 20px;
            background-color: white;
            border-radius: 10px;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
        }
        header {
            background-color: #ff6347; /* Tomato Red Header */
            color: white;
            padding: 15px 20px;
            border-radius: 8px;
            margin-bottom: 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        header h1 {
            cursor: pointer;
            font-size: 24px;
            font-weight: 700;
        }
        .button {
            padding: 8px 15px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-weight: 700;
            transition: background-color 0.2s;
        }
        .primary-button {
            background-color: #4682b4; /* Steel Blue Primary */
            color: white;
        }
        .primary-button:hover {
            background-color: #5f9ea0;
        }
        .product-card {
            border: 1px solid #ccc;
            padding: 15px;
            margin-bottom: 20px;
            border-radius: 8px;
            background-color: #fffaf0; /* Light Yellow Card */
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.05);
            display: flex;
            flex-direction: column;
            text-align: center;
        }
        .product-card img {
            width: 100%;
            height: 150px;
            object-fit: cover;
            border-radius: 5px;
            margin-bottom: 10px;
        }
        .product-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 20px;
        }
        .cart-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 15px 0;
            border-bottom: 1px dashed #ddd;
        }
        .input-quantity {
            width: 50px;
            text-align: center;
            padding: 5px;
            border: 1px solid #ccc;
            border-radius: 4px;
        }
    </style>
</head>
<body>

    <div id="app" class="container">
        <!-- Header/Navigation -->
        <header>
            <h1 onclick="navigate('products')">Simple Bharat Gadgets</h1>
            <button id="cart-button" onclick="navigate('cart')" class="button" style="background-color: #ffd700; color: #333; position: relative;">
                Cart (<span id="cart-count">0</span>)
                <!-- Simple badge styling via inline CSS -->
                <span id="cart-badge" style="position: absolute; top: -5px; right: -5px; background-color: #f00; color: white; border-radius: 50%; padding: 2px 6px; font-size: 10px; display: none;">0</span>
            </button>
        </header>

        <!-- Main Content View Container -->
        <main id="content">
            <!-- Content will be rendered here by JavaScript -->
            <p>Loading store data...</p>
        </main>
    </div>

    <script type="text/javascript">
        // Global State Variables (In-memory state for simplicity)
        window.currentView = 'products';
        window.cartItems = [];

        // --- Product Data ---
        const products = [
            { id: 'P001', name: 'Premium Noise-Cancelling Headphones', price: 8999, description: 'Experience pure sound with crystal-clear audio and deep bass.', image: 'https://placehold.co/400x300/e0ffff/4682b4?text=HEADPHONES' },
            { id: 'P002', name: '4K Smart LED Projector', price: 34999, description: 'Turn any wall into a theatre with stunning resolution and brightness.', image: 'https://placehold.co/400x300/90ee90/3cb371?text=PROJECTOR' },
            { id: 'P003', name: 'Ultra-Thin Power Bank (20000mAh)', price: 1999, description: 'Fast charging on the go. Sleek design, massive capacity.', image: 'https://placehold.co/400x300/ffb6c1/db7093?text=POWER+BANK' },
            { id: 'P004', name: 'Ergonomic Mechanical Keyboard', price: 4200, description: 'Tactile switches and customizable RGB lighting for peak performance.', image: 'https://placehold.co/400x300/add8e6/4682b4?text=KEYBOARD' },
        ];

        // --- Utility Functions ---

        /**
         * Formats a number into Indian Rupees (₹) currency string.
         */
        const formatINR = (amount) => {
            if (typeof amount !== 'number') return '₹0';
            return new Intl.NumberFormat('en-IN', {
                style: 'currency',
                currency: 'INR',
                maximumFractionDigits: 0
            }).format(amount);
        };

        /**
         * Finds a product by ID.
         */
        const getProductById = (id) => products.find(p => p.id === id);

        // --- Cart Management ---

        /**
         * Adds a product to the cart or increments its quantity.
         */
        const addToCart = (productId) => {
            const product = getProductById(productId);
            if (!product) {
                console.error("Product not found:", productId);
                return;
            }

            const existingItem = window.cartItems.find(item => item.productId === productId);

            if (existingItem) {
                existingItem.quantity += 1;
            } else {
                window.cartItems.push({
                    productId: product.id,
                    name: product.name,
                    price: product.price,
                    quantity: 1,
                    image: product.image
                });
            }
            window.renderApp();
        };

        /**
         * Updates the quantity of a cart item.
         */
        const updateCartQuantity = (productId, newQuantity) => {
            const quantity = parseInt(newQuantity, 10);
            
            // Validate quantity
            if (isNaN(quantity) || quantity < 1) {
                removeFromCart(productId);
                return;
            }

            const item = window.cartItems.find(item => item.productId === productId);
            if (item) {
                item.quantity = quantity;
            }
            window.renderApp();
        };

        /**
         * Removes a product completely from the cart.
         */
        const removeFromCart = (productId) => {
            window.cartItems = window.cartItems.filter(item => item.productId !== productId);
            window.renderApp();
        };


        // --- View Navigation & Rendering ---

        /**
         * Changes the current application view.
         */
        const navigate = (view) => {
            window.currentView = view;
            window.renderApp();
            window.scrollTo(0, 0); 
        };

        /**
         * Main render function that updates the UI based on state.
         */
        window.renderApp = () => {
            const contentDiv = document.getElementById('content');
            const cartCountSpan = document.getElementById('cart-count');
            const cartBadgeSpan = document.getElementById('cart-badge');
            
            const totalItems = window.cartItems.reduce((sum, item) => sum + item.quantity, 0);
            
            // Update cart count and badge visibility
            cartCountSpan.textContent = totalItems;
            cartBadgeSpan.textContent = totalItems;
            cartBadgeSpan.style.display = totalItems > 0 ? 'inline-block' : 'none';
            
            switch (window.currentView) {
                case 'products':
                    contentDiv.innerHTML = renderProductsView();
                    break;
                case 'cart':
                    contentDiv.innerHTML = renderCartView();
                    break;
                case 'checkout':
                    contentDiv.innerHTML = renderCheckoutView();
                    break;
                case 'order_placed':
                    contentDiv.innerHTML = renderOrderPlacedView();
                    break;
                default:
                    contentDiv.innerHTML = renderProductsView();
            }
        };

        // --- View Generators ---

        function renderProductsView() {
            const cards = products.map(p => `
                <div class="product-card">
                    <img src="${p.image}" alt="${p.name}" style="border: 2px solid #4682b4;">
                    <h2 style="font-size: 18px; color: #4682b4; margin-bottom: 5px;">${p.name}</h2>
                    <p style="font-size: 12px; color: #666; height: 30px; overflow: hidden; margin-bottom: 10px;">${p.description}</p>
                    <p style="font-size: 20px; font-weight: 700; color: #3cb371; margin-bottom: 15px;">${formatINR(p.price)}</p>
                    <button onclick="addToCart('${p.id}')"
                            class="button primary-button">
                        Add to Cart
                    </button>
                </div>
            `).join('');

            return `
                <h1 style="color: #ff6347; border-bottom: 2px solid #ff6347; padding-bottom: 10px; margin-bottom: 20px;">Our Beginner Gadgets</h1>
                <div class="product-grid">
                    ${cards}
                </div>
            `;
        }

        function renderCartView() {
            if (window.cartItems.length === 0) {
                return `
                    <div style="text-align: center; padding: 50px;">
                        <h2 style="color: #4682b4;">Your Cart is Empty</h2>
                        <p>Time to add some colorful gadgets!</p>
                        <button onclick="navigate('products')" class="button primary-button" style="margin-top: 20px;">
                            Go to Store
                        </button>
                    </div>
                `;
            }

            let subtotal = 0;
            const cartList = window.cartItems.map(item => {
                const itemTotal = item.price * item.quantity;
                subtotal += itemTotal;
                return `
                    <div class="cart-item">
                        <div style="display: flex; align-items: center; gap: 10px; width: 50%;">
                            <img src="${item.image}" alt="${item.name}" style="width: 50px; height: 50px; object-fit: cover; border-radius: 5px; border: 1px solid #ccc;">
                            <span style="font-weight: 700;">${item.name}</span>
                        </div>

                        <div style="display: flex; align-items: center; gap: 10px;">
                            <!-- Quantity controls -->
                            <input type="number" value="${item.quantity}" min="1" 
                                onchange="updateCartQuantity('${item.productId}', this.value)"
                                class="input-quantity" style="background-color: #e0ffff;">
                            
                            <!-- Item Total -->
                            <span style="font-weight: 700; color: #4682b4; width: 80px; text-align: right;">${formatINR(itemTotal)}</span>

                            <!-- Remove Button (Inline CSS for color) -->
                            <button onclick="removeFromCart('${item.productId}')" class="button" 
                                style="background-color: #f08080; color: white; padding: 5px 10px; font-size: 12px;">
                                Remove
                            </button>
                        </div>
                    </div>
                `;
            }).join('');

            const taxRate = 0.05; 
            const shippingCost = subtotal > 15000 ? 0 : 500;
            const tax = subtotal * taxRate;
            const grandTotal = subtotal + tax + shippingCost;

            return `
                <h1 style="color: #ff6347; border-bottom: 2px solid #ff6347; padding-bottom: 10px; margin-bottom: 20px;">Your Shopping Cart</h1>
                
                <div style="display: flex; flex-direction: column; gap: 20px;">
                    <!-- Cart List -->
                    <div style="flex: 3;">${cartList}</div>

                    <!-- Summary Card (Inline CSS for a colorful box) -->
                    <div style="flex: 2; background-color: #fff0f5; padding: 20px; border-radius: 8px; border: 2px solid #db7093;">
                        <h2 style="font-size: 20px; color: #db7093; border-bottom: 1px solid #db7093; padding-bottom: 10px; margin-bottom: 15px;">Order Summary</h2>
                        
                        <div style="display: flex; justify-content: space-between; margin-bottom: 5px;">
                            <span>Subtotal:</span>
                            <span>${formatINR(subtotal)}</span>
                        </div>
                        <div style="display: flex; justify-content: space-between; margin-bottom: 5px;">
                            <span>GST (5%):</span>
                            <span>${formatINR(tax)}</span>
                        </div>
                        <div style="display: flex; justify-content: space-between; margin-bottom: 15px; font-weight: 700;">
                            <span>Shipping:</span>
                            <span style="color: ${shippingCost === 0 ? '#3cb371' : '#4682b4'};">${shippingCost === 0 ? 'FREE' : formatINR(shippingCost)}</span>
                        </div>

                        <div style="display: flex; justify-content: space-between; border-top: 2px solid #db7093; padding-top: 15px;">
                            <span style="font-size: 20px; font-weight: 700;">Grand Total:</span>
                            <span style="font-size: 24px; font-weight: 700; color: #ff6347;">${formatINR(grandTotal)}</span>
                        </div>

                        <button onclick="navigate('checkout')"
                                class="button" style="width: 100%; margin-top: 20px; background-color: #3cb371; color: white;">
                            Proceed to Checkout
                        </button>
                    </div>
                </div>
            `;
        }

        function renderCheckoutView() {
            const subtotal = window.cartItems.reduce((sum, item) => sum + (item.price * item.quantity), 0);
            const taxRate = 0.05;
            const shippingCost = subtotal > 15000 ? 0 : 500;
            const tax = subtotal * taxRate;
            const grandTotal = subtotal + tax + shippingCost;

            return `
                <h1 style="color: #4682b4; border-bottom: 2px solid #4682b4; padding-bottom: 10px; margin-bottom: 20px;">Checkout Details</h1>
                <div style="display: flex; flex-wrap: wrap; gap: 20px;">
                    
                    <!-- Shipping Form -->
                    <div style="flex: 3; min-width: 300px;">
                        <h2 style="color: #db7093; margin-bottom: 15px;">Shipping Information (India)</h2>
                        <form id="checkout-form" style="display: flex; flex-direction: column; gap: 10px; background-color: #e0ffff; padding: 20px; border-radius: 8px;">
                            <label for="name">Full Name:</label>
                            <input type="text" id="name" required style="padding: 8px; border: 1px solid #ccc; border-radius: 4px;">

                            <label for="phone">Mobile Number (10 digits):</label>
                            <input type="tel" id="phone" required pattern="[0-9]{10}" style="padding: 8px; border: 1px solid #ccc; border-radius: 4px;">

                            <label for="address1">Address Line 1:</label>
                            <input type="text" id="address1" required style="padding: 8px; border: 1px solid #ccc; border-radius: 4px;">

                            <label for="pincode">Pincode (6 digits):</label>
                            <input type="text" id="pincode" required pattern="[0-9]{6}" style="padding: 8px; border: 1px solid #ccc; border-radius: 4px;">

                            <label for="city">City/District:</label>
                            <input type="text" id="city" required style="padding: 8px; border: 1px solid #ccc; border-radius: 4px;">
                            
                            <label for="state">State:</label>
                            <select id="state" required style="padding: 8px; border: 1px solid #ccc; border-radius: 4px;">
                                <option value="">Select State</option>
                                <option value="MH">Maharashtra</option>
                                <option value="DL">Delhi</option>
                                <option value="KA">Karnataka</option>
                                <option value="TN">Tamil Nadu</option>
                                <option value="UP">Uttar Pradesh</option>
                                <!-- Basic list for simplicity -->
                            </select>
                            
                            <h2 style="color: #db7093; margin-top: 20px; margin-bottom: 5px;">Payment</h2>
                            <label style="background-color: white; padding: 10px; border-radius: 5px; border: 1px solid #4682b4;">
                                <input type="radio" name="payment" value="cod" checked>
                                Cash On Delivery (COD)
                            </label>
                        </form>
                    </div>

                    <!-- Final Summary -->
                    <div style="flex: 2; min-width: 200px; background-color: #f0fff0; padding: 20px; border-radius: 8px; border: 2px solid #3cb371; height: fit-content;">
                        <h2 style="font-size: 20px; color: #3cb371; border-bottom: 1px solid #3cb371; padding-bottom: 10px; margin-bottom: 15px;">Final Summary</h2>
                        
                        <div style="display: flex; justify-content: space-between; margin-bottom: 5px;"><span>Subtotal:</span><span>${formatINR(subtotal)}</span></div>
                        <div style="display: flex; justify-content: space-between; margin-bottom: 5px;"><span>GST (5%):</span><span>${formatINR(tax)}</span></div>
                        <div style="display: flex; justify-content: space-between; margin-bottom: 15px;"><span>Shipping:</span><span>${shippingCost === 0 ? 'FREE' : formatINR(shippingCost)}</span></div>

                        <div style="display: flex; justify-content: space-between; border-top: 2px solid #3cb371; padding-top: 15px;">
                            <span style="font-size: 20px; font-weight: 700;">Total Payable:</span>
                            <span style="font-size: 24px; font-weight: 700; color: #ff6347;">${formatINR(grandTotal)}</span>
                        </div>

                        <button onclick="placeOrder()"
                                class="button" style="width: 100%; margin-top: 20px; background-color: #ff6347; color: white;">
                            PLACE ORDER NOW
                        </button>
                    </div>
                </div>
            `;
        }

        /**
         * Simulates placing the order, clears the cart, and navigates.
         */
        const placeOrder = () => {
            const form = document.getElementById('checkout-form');
            if (!form.checkValidity()) {
                // If form is invalid, this triggers the browser's default validation messages
                form.reportValidity();
                return;
            }

            // Clear the cart state
            window.cartItems = [];
            
            // Navigate to the success page
            navigate('order_placed');
        };

        function renderOrderPlacedView() {
            return `
                <div style="text-align: center; padding: 50px; background-color: #f0fff0; border: 2px solid #3cb371; border-radius: 10px;">
                    <h2 style="font-size: 24px; color: #3cb371; margin-bottom: 10px;">Order Placed Successfully!</h2>
                    <p style="font-size: 16px; color: #666; margin-bottom: 20px;">Your simple order is confirmed. Thank you!</p>
                    <button onclick="navigate('products')" class="button primary-button">
                        Continue Shopping
                    </button>
                </div>
            `;
        }

        // Initial render call when the page loads
        document.addEventListener('DOMContentLoaded', () => {
             window.renderApp();
        });

    </script>
</body>
</html>
