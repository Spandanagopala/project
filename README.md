<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Agrilink Crop Marketplace Flow</title>
    <!-- Load Tailwind CSS --><script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Custom font */
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f4f7f6;
        }
    </style>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        'primary': '#059669', /* Green */
                        'secondary': '#fbbf24', /* Amber */
                        'background': '#f4f7f6',
                        'card': '#ffffff',
                    }
                }
            }
        }
    </script>
</head>
<body class="min-h-screen flex items-start justify-center p-4">

    <!-- Main Container --><div id="app" class="w-full max-w-xl bg-background shadow-2xl rounded-xl p-6 md:p-8">
        <header class="mb-6 text-center">
            <h1 class="text-3xl font-extrabold text-primary cursor-pointer" onclick="goToScreen('homeScreen')">Agrilink App</h1>
            <p class="text-gray-500 mt-1">Marketplace Workflow</p>
        </header>

        <!-- Dynamic Content Area --><div id="screen-container" class="bg-card p-6 rounded-lg shadow-inner min-h-[400px]">
            <!-- Content will be injected here by JavaScript --></div>

        <!-- Notification Message Box --><div id="message-box" class="mt-4 hidden p-3 rounded-lg text-sm transition-all duration-300"></div>

    </div>

    <script>
        // =================================================================
        // JAVASCRIPT APP LOGIC
        // =================================================================

        const appState = {
            currentScreen: 'homeScreen',
            currentUser: null, // { role: 'customer' | 'farmer', name: 'User' }
            searchQuery: '', 
            mockDB: { // Simulating TinyDB (Backend Storage)
                products: [
                    // Prices updated to more typical INR values
                    // ADDED: Emojis for each product
                    { id: 1, name: 'Organic Tomatoes', emoji: '🍅', price: 80.00, unit: 'kg', farmer: 'Farm A' },
                    { id: 2, name: 'Heirloom Carrots', emoji: '🥕', price: 55.00, unit: 'kg', farmer: 'Farm B' },
                    { id: 3, name: 'Fresh Strawberries', emoji: '🍓', price: 350.00, unit: 'box', farmer: 'Farm C' },
                    { id: 4, name: 'Red Onions', emoji: '🧅', price: 40.00, unit: 'kg', farmer: 'Farm A' },
                    { id: 5, name: 'Potatoes', emoji: '🥔', price: 30.00, unit: 'kg', farmer: 'Farm D' },
                ],
            },
            cart: []
        };

        const container = document.getElementById('screen-container');
        const messageBox = document.getElementById('message-box');

        // Utility function to display success/error messages
        function showMessage(text, type = 'success') {
            messageBox.textContent = text;
            messageBox.classList.remove('hidden', 'bg-red-100', 'text-red-800', 'bg-green-100', 'text-green-800');

            if (type === 'success') {
                messageBox.classList.add('bg-green-100', 'text-green-800');
            } else if (type === 'error') {
                messageBox.classList.add('bg-red-100', 'text-red-800');
            }
            setTimeout(() => {
                messageBox.classList.add('hidden');
            }, 3000);
        }

        // Global handler to update search state and re-render the Product Page
        window.handleSearch = (query) => {
            appState.searchQuery = query;
            renderProductPage(); // Re-render the page with the new filter
        };


        // =================================================================
        // SCREEN RENDERERS
        // =================================================================

        function renderHome() {
            // Clear search query when going home
            appState.searchQuery = ''; 
            
            container.innerHTML = `
                <h2 class="text-2xl font-semibold text-gray-800 mb-4 text-center">Welcome to Agrilink</h2>
                <p class="text-gray-600 mb-8 text-center font-medium">
                    Agrilink is your platform to connect *farmers directly with consumers*, ensuring fair prices and fresh, high-quality produce. Choose your role below to start trading.
                </p>

                <div class="space-y-4">
                    <!-- Buy Crops (Customer Login) --><button onclick="goToScreen('loginScreen', {role: 'customer'})"
                        class="w-full flex items-center justify-center p-4 bg-primary text-white font-bold rounded-lg shadow-md hover:bg-green-700 transition duration-150 transform hover:scale-[1.02]">
                        🛒 Buy Crops (Customer)
                    </button>

                    <!-- Sell Crops (Farmer Login) --><button onclick="goToScreen('loginScreen', {role: 'farmer'})"
                        class="w-full flex items-center justify-center p-4 bg-secondary text-gray-800 font-bold rounded-lg shadow-md hover:bg-amber-600 transition duration-150 transform hover:scale-[1.02]">
                        🌾 Sell Crops (Farmer)
                    </button>
                </div>
            `;
        }

        function renderLogin(role) {
            const roleName = role.charAt(0).toUpperCase() + role.slice(1);
            container.innerHTML = `
                <h2 class="text-2xl font-semibold text-gray-800 mb-4 text-center">${roleName} Login</h2>
                <p class="text-gray-600 mb-6 text-center">Enter credentials to log in.</p>

                <div class="space-y-4">
                    <div>
                        <label for="username" class="block text-sm font-medium text-gray-700">Username (Mock)</label>
                        <input type="text" id="username" value="${role}user"
                                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 border focus:border-primary focus:ring-primary"
                                placeholder="Enter username">
                    </div>
                    <div>
                        <label for="password" class="block text-sm font-medium text-gray-700">Password (Mock)</label>
                        <input type="password" id="password" value="password"
                                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 border focus:border-primary focus:ring-primary"
                                placeholder="Enter password">
                    </div>

                    <button onclick="mockLogin('${role}')"
                        class="w-full p-3 bg-primary text-white font-semibold rounded-lg shadow-md hover:bg-green-700 transition duration-150">
                        Login Success
                    </button>
                    
                    <!-- New Sign Up Link --><p class="text-center text-sm">
                        Don't have an account?
                        <button onclick="goToScreen('signupScreen', {role: '${role}'})" class="text-secondary font-medium hover:underline">
                            Sign Up
                        </button>
                    </p>

                    <button onclick="goToScreen('homeScreen')"
                        class="w-full p-3 text-primary border border-primary rounded-lg hover:bg-primary/10 transition duration-150">
                        ← Back to Home
                    </button>
                </div>
            `;
        }
        
        // SIGN UP SCREEN RENDERER
        function renderSignup(role) {
            const roleName = role.charAt(0).toUpperCase() + role.slice(1);
            container.innerHTML = `
                <h2 class="text-2xl font-semibold text-gray-800 mb-4 text-center">${roleName} Sign Up</h2>
                <p class="text-gray-600 mb-6 text-center">Enter details to create a new user account.</p>

                <div class="space-y-4">
                    <div>
                        <label for="username" class="block text-sm font-medium text-gray-700">New Username (Mock)</label>
                        <input type="text" id="username" value="${role}newuser"
                                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 border focus:border-primary focus:ring-primary"
                                placeholder="Enter username">
                    </div>
                    <div>
                        <label for="password" class="block text-sm font-medium text-gray-700">New Password (Mock)</label>
                        <input type="password" id="password" value="newpassword"
                                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 border focus:border-primary focus:ring-primary"
                                placeholder="Enter password">
                    </div>

                    <button onclick="mockSignup('${role}')"
                        class="w-full p-3 bg-secondary text-gray-800 font-semibold rounded-lg shadow-md hover:bg-amber-600 transition duration-150">
                        Sign Up Success
                    </button>

                    <p class="text-center text-sm">
                        Already have an account?
                        <button onclick="goToScreen('loginScreen', {role: '${role}'})" class="text-primary font-medium hover:underline">
                            Log In
                        </button>
                    </p>
                    
                    <button onclick="goToScreen('homeScreen')"
                        class="w-full p-3 text-primary border border-primary rounded-lg hover:bg-primary/10 transition duration-150">
                        ← Back to Home
                    </button>
                </div>
            `;
        }

        function mockLogin(role) {
            appState.currentUser = { role: role, name: document.getElementById('username').value || ${role}User };
            showMessage(Login Successful! Logged in as ${appState.currentUser.name} (${role})., 'success');

            if (role === 'customer') {
                goToScreen('productPage'); // Customer -> Product Page
            } else if (role === 'farmer') {
                goToScreen('addProductPage'); // Farmer -> Add Product Page
            }
        }
        
        // SIGN UP HANDLER
        function mockSignup(role) {
            appState.currentUser = { role: role, name: document.getElementById('username').value || ${role}NewUser };
            showMessage(Signup Successful! Logged in as ${appState.currentUser.name} (${role})., 'success');

            if (role === 'customer') {
                goToScreen('productPage'); // Customer -> Product Page
            } else if (role === 'farmer') {
                goToScreen('addProductPage'); // Farmer -> Add Product Page
            }
        }


        // --- CUSTOMER FLOW ---

        function renderProductPage() {
            if (!appState.currentUser) return goToScreen('homeScreen'); // Guard

            const query = appState.searchQuery.toLowerCase();
            
            // FILTERING LOGIC: filter products based on name or farmer
            const filteredProducts = appState.mockDB.products.filter(p => 
                p.name.toLowerCase().includes(query) || 
                p.farmer.toLowerCase().includes(query)
            );

            const productListHTML = filteredProducts.length > 0 ? filteredProducts.map(p => `
                <div class="flex justify-between items-center p-4 border-b last:border-b-0">
                    <div>
                        <!-- Display emoji next to product name --><p class="font-semibold text-gray-900">${p.emoji} ${p.name}</p>
                        <p class="text-sm text-gray-500">Farm: ${p.farmer} | ₹${p.price.toFixed(2)} / ${p.unit}</p>
                    </div>
                    <button onclick="addToCart(${p.id}, '${p.name}')"
                        class="px-3 py-1 bg-primary text-white rounded-md text-sm font-medium hover:bg-green-600 transition">
                        Select Crop / Add to Cart
                    </button>
                </div>
            `).join('') : '<p class="text-center text-gray-500 py-10">No crops found matching your search criteria.</p>';

            container.innerHTML = `
                <h2 class="text-2xl font-semibold text-gray-800 mb-4 text-center">Product Page (Customer View)</h2>
                <p class="text-gray-600 mb-4 text-center">Choose a crop to add to your cart. (Fetching Products from Database)</p>
                
                <!-- Search Bar with global handler --><input type="text" id="productSearch" placeholder="Search by crop name or farmer..."
                    class="mb-4 block w-full rounded-md border-gray-300 shadow-sm p-3 border focus:border-primary focus:ring-primary"
                    value="${appState.searchQuery}" onkeyup="handleSearch(this.value)">

                <div class="border rounded-lg mb-6 max-h-60 overflow-y-auto">
                    ${productListHTML}
                </div>

                <div class="space-y-3">
                    <p class="text-center font-medium text-gray-700">Current Cart Items: <span class="text-primary">${appState.cart.length}</span></p>
                    <button onclick="goToScreen('cartPage')"
                        class="w-full p-3 bg-secondary text-gray-800 font-semibold rounded-lg shadow-md hover:bg-amber-600 transition duration-150">
                        Go to Cart (${appState.cart.length})
                    </button>
                </div>
            `;
            // Set focus on the search bar after re-render for better UX
            const searchInput = document.getElementById('productSearch');
            if(searchInput) {
                searchInput.focus();
            }
        }

        function addToCart(productId, productName) {
            const product = appState.mockDB.products.find(p => p.id === productId);
            if (product) {
                appState.cart.push({ ...product, quantity: 1, cartId: Date.now() });
                showMessage(${productName} added to cart! (Saved to Database));
                renderProductPage(); // Re-render to update cart count
            }
        }

        function renderCartPage() {
            if (!appState.currentUser) return goToScreen('homeScreen'); // Guard

            const total = appState.cart.reduce((sum, item) => sum + (item.price * item.quantity), 0);

            const cartListHTML = appState.cart.length > 0 ? appState.cart.map(item => `
                <div class="flex justify-between items-center p-4 border-b last:border-b-0 bg-gray-50 rounded-md mb-2">
                    <div>
                        <!-- Display emoji next to cart item name --><p class="font-medium text-gray-900">${item.emoji} ${item.name} x 1</p>
                        <p class="text-sm text-gray-500">₹${item.price.toFixed(2)}</p>
                    </div>
                </div>
            `).join('') : '<p class="text-center text-gray-500 py-10">Your cart is empty. Go add some crops!</p>';

            container.innerHTML = `
                <h2 class="text-2xl font-semibold text-gray-800 mb-4 text-center">Cart Page</h2>
                <p class="text-gray-600 mb-6 text-center">Review your order. (Retrieving Cart Items)</p>

                <div class="border rounded-lg mb-6 p-4 max-h-60 overflow-y-auto">
                    ${cartListHTML}
                </div>

                <div class="flex justify-between items-center p-3 border-t-2 border-primary/50 text-xl font-bold mb-6">
                    <span>Total:</span>
                    <span>₹${total.toFixed(2)}</span>
                </div>

                <div class="space-y-3">
                    <button onclick="goToScreen('checkoutDetailsPage')"
                        class="w-full p-3 bg-primary text-white font-semibold rounded-lg shadow-md hover:bg-green-700 transition duration-150"
                        ${appState.cart.length === 0 ? 'disabled' : ''}>
                        Checkout & Select Payment
                    </button>
                    <button onclick="goToScreen('productPage')"
                        class="w-full p-3 text-primary border border-primary rounded-lg hover:bg-primary/10 transition duration-150">
                        ← Continue Shopping
                    </button>
                </div>
            `;
        }
        
        // --- NEW CHECKOUT DETAILS PAGE ---
        function renderCheckoutDetailsPage() {
            if (!appState.currentUser) return goToScreen('homeScreen');

            const finalTotal = appState.cart.reduce((sum, item) => sum + (item.price * item.quantity), 0);

            container.innerHTML = `
                <h2 class="text-2xl font-semibold text-gray-800 mb-4 text-center">Select Payment Method</h2>
                <p class="text-gray-600 mb-6 text-center">How would you like to pay for your order?</p>

                <div class="p-4 bg-green-50 border border-green-200 rounded-lg mb-6 flex justify-between items-center">
                    <p class="text-lg font-medium text-gray-700">Order Total:</p>
                    <p class="text-2xl font-extrabold text-primary">₹${finalTotal.toFixed(2)}</p>
                </div>

                <div class="space-y-4">
                    <!-- Mock Payment Options -->
                    <div class="p-3 border rounded-lg hover:border-primary cursor-pointer transition duration-150 bg-white shadow-sm">
                        <label class="flex items-center space-x-3">
                            <input type="radio" name="paymentMethod" value="UPI" checked class="form-radio text-primary h-4 w-4">
                            <span class="text-gray-900 font-medium">📱 UPI / Net Banking</span>
                        </label>
                    </div>
                    <div class="p-3 border rounded-lg hover:border-primary cursor-pointer transition duration-150 bg-white shadow-sm">
                        <label class="flex items-center space-x-3">
                            <input type="radio" name="paymentMethod" value="Card" class="form-radio text-primary h-4 w-4">
                            <span class="text-gray-900 font-medium">💳 Credit / Debit Card</span>
                        </label>
                    </div>
                    <div class="p-3 border rounded-lg hover:border-primary cursor-pointer transition duration-150 bg-white shadow-sm">
                        <label class="flex items-center space-x-3">
                            <input type="radio" name="paymentMethod" value="COD" class="form-radio text-primary h-4 w-4">
                            <span class="text-gray-900 font-medium">💵 Cash on Delivery (COD)</span>
                        </label>
                    </div>

                    <button onclick="goToScreen('paymentPage')"
                        class="w-full p-3 mt-6 bg-primary text-white font-semibold rounded-lg shadow-md hover:bg-green-700 transition duration-150">
                        Confirm Order & Pay
                    </button>
                    <button onclick="goToScreen('cartPage')"
                        class="w-full p-3 text-gray-500 border border-gray-300 rounded-lg hover:bg-gray-100 transition duration-150">
                        ← Back to Cart
                    </button>
                </div>
            `;
        }

        function renderPaymentPage() {
            if (!appState.currentUser) return goToScreen('homeScreen'); // Guard

            const finalTotal = appState.cart.reduce((sum, item) => sum + (item.price * item.quantity), 0);

            container.innerHTML = `
                <h2 class="text-2xl font-semibold text-gray-800 mb-4 text-center">Order Confirmation</h2>
                <p class="text-gray-600 mb-6 text-center">Thank you! Your transaction is complete. (Order Placed)</p>

                <div class="p-6 bg-green-50 border border-green-200 rounded-lg mb-6">
                    <p class="text-lg font-medium text-gray-700 mb-2">Order Summary:</p>
                    <p class="text-3xl font-extrabold text-primary text-center">₹${finalTotal.toFixed(2)}</p>
                    <p class="text-center text-sm text-gray-500 mt-1">${appState.cart.length} unique items</p>
                </div>

                <div class="space-y-4">
                    <p class="text-center text-lg font-bold text-red-500">
                        <span class="text-primary">Payment Successful!</span> (Transaction Complete)
                    </p>
                    <p class="text-sm text-gray-500 text-center">
                        In a real application, this would confirm payment and clear the cart.
                    </p>
                    <button onclick="resetAppAndGoHome()"
                        class="w-full p-3 bg-gray-500 text-white font-semibold rounded-lg shadow-md hover:bg-gray-700 transition duration-150">
                        Start New Session (Back to Home)
                    </button>
                </div>
            `;
        }

        // --- FARMER FLOW ---

        function renderAddProductPage() {
            if (!appState.currentUser || appState.currentUser.role !== 'farmer') return goToScreen('homeScreen'); // Guard

            container.innerHTML = `
                <h2 class="text-2xl font-semibold text-gray-800 mb-4 text-center">Add Product Page (Farmer View)</h2>
                <p class="text-gray-600 mb-6 text-center">Enter crop details to make them available. (Storing Product in Database)</p>

                <form id="addProductForm" class="space-y-4">
                    <div>
                        <label for="cropName" class="block text-sm font-medium text-gray-700">Crop Name</label>
                        <input type="text" id="cropName" required
                                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 border focus:border-primary focus:ring-primary">
                    </div>
                    <div>
                        <label for="emoji" class="block text-sm font-medium text-gray-700">Emoji (e.g., 🍎, 🧅)</label>
                        <input type="text" id="emoji" maxlength="2"
                                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 border focus:border-primary focus:ring-primary"
                                placeholder="Optional emoji for the product">
                    </div>
                    <div>
                        <label for="price" class="block text-sm font-medium text-gray-700">Price (per unit in ₹)</label>
                        <input type="number" id="price" required min="0.01" step="0.01"
                                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 border focus:border-primary focus:ring-primary">
                    </div>
                    <div>
                        <label for="unit" class="block text-sm font-medium text-gray-700">Unit (e.g., kg, lb, box)</label>
                        <input type="text" id="unit" required
                                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 border focus:border-primary focus:ring-primary">
                    </div>

                    <button type="submit"
                        class="w-full p-3 bg-secondary text-gray-800 font-semibold rounded-lg shadow-md hover:bg-amber-600 transition duration-150">
                        Save Product (Store Product)
                    </button>
                    <button type="button" onclick="goToScreen('homeScreen')"
                        class="w-full p-3 text-gray-500 border border-gray-300 rounded-lg hover:bg-gray-100 transition duration-150">
                        ← Logout / Back to Home
                    </button>
                </form>
            `;
            document.getElementById('addProductForm').onsubmit = handleAddProduct;
        }

        function handleAddProduct(event) {
            event.preventDefault();
            const name = document.getElementById('cropName').value;
            // ADDED: Get emoji input
            const emoji = document.getElementById('emoji').value.trim(); 
            const price = parseFloat(document.getElementById('price').value);
            const unit = document.getElementById('unit').value;

            // Simulates 'Store Product' to TinyDB
            const newProduct = {
                id: Date.now(), // unique ID
                name: name,
                emoji: emoji || '🌱', // Default emoji if none provided
                price: price,
                unit: unit,
                farmer: appState.currentUser.name
            };

            appState.mockDB.products.push(newProduct);
            showMessage(New product '${name}' added successfully! (Stored in Database));

            // Optionally, clear the form or re-render
            document.getElementById('addProductForm').reset();
            // Optionally: goToScreen('productPage') if we wanted to show all products immediately.
        }

        // =================================================================
        // NAVIGATION & INIT
        // =================================================================

        function goToScreen(screenName, params = {}) {
            // Update the state
            appState.currentScreen = screenName;

            // Clear previous messages
            messageBox.classList.add('hidden');

            // Render the new screen
            switch (screenName) {
                case 'homeScreen':
                    renderHome();
                    break;
                case 'loginScreen':
                    renderLogin(params.role);
                    break;
                case 'signupScreen': 
                    renderSignup(params.role);
                    break;
                case 'productPage':
                    renderProductPage();
                    break;
                case 'cartPage':
                    renderCartPage();
                    break;
                case 'checkoutDetailsPage':
                    renderCheckoutDetailsPage();
                    break;
                case 'paymentPage':
                    renderPaymentPage();
                    break;
                case 'addProductPage':
                    renderAddProductPage();
                    break;
                default:
                    renderHome();
            }
            // Scroll to top of container for better UX on navigation
            container.parentElement.scrollTo(0, 0);
        }

        function resetAppAndGoHome() {
            appState.currentUser = null;
            appState.cart = [];
            // Reset search query when resetting the app
            appState.searchQuery = ''; 
            goToScreen('homeScreen');
            showMessage("Session ended. Welcome back!", 'success');
        }

        // Initial load
        window.onload = () => {
            // Ensure initial display is Home Screen
            goToScreen('homeScreen');
        };

    </script>
</body>
</html>
