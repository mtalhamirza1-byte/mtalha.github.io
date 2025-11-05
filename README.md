<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>BubbleBurst Laundry</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      margin: 0;
      padding: 0;
      background: #f6fafd;
      color: #222;
    }

    header {
      background: #2196f3;
      color: white;
      padding: 1rem;
      text-align: center;
      position: sticky;
      top: 0;
      z-index: 10;
    }

    .currency-selector {
      margin-top: 0.5rem;
    }

    main {
      padding: 2rem 1rem 1rem 1rem;
      max-width: 600px;
      margin: auto;
    }

    .laundry-form-section {
      background: #fff;
      border-radius: 10px;
      box-shadow: 0 3px 12px rgba(33,150,243,0.06);
      padding: 1.5rem;
      margin-bottom: 2rem;
    }

    .laundry-form-section h2 {
      margin-top: 0;
    }

    #laundryForm {
      display: flex;
      flex-direction: column;
      gap: 1rem;
    }

    #laundryForm input, #laundryForm textarea, #laundryForm button {
      font-size: 1rem;
      padding: 0.75rem;
      border-radius: 6px;
      border: 1px solid #ccc;
      outline: none;
    }

    #laundryForm button {
      background: #2196f3;
      color: white;
      border: none;
      cursor: pointer;
      transition: background 0.2s;
    }
    #laundryForm button:hover {
      background: #1769aa;
    }

    section > h2 {
      border-bottom: 2px solid #2196f3;
      padding-bottom: 0.3rem;
      margin-bottom: 1rem;
    }

    #ordersList {
      list-style: none;
      padding: 0;
    }

    .order-item {
      background: #e3f2fd;
      margin-bottom: 1rem;
      padding: 1rem;
      border-radius: 8px;
      box-shadow: 0 1px 6px #2196f366;
    }

    .order-item span.currency {
      font-weight: bold;
      color: #1769aa;
    }

    footer {
      text-align: center;
      padding: 1.5rem 1rem;
      background: #2196f3;
      color: white;
      margin-top: 2rem;
    }
  </style>
</head>
<body>
  <header>
    <h1>BubbleBurst Laundry</h1>
    <div class="currency-selector">
      <label for="currency">Choose Currency:</label>
      <select id="currency">
        <option value="USD" selected>USD ($) - USA</option>
        <option value="AED">AED (د.إ) - UAE</option>
        <option value="SAR">SAR (ر.س) - Saudi Arabia</option>
        <option value="PKR">PKR (₨) - Pakistan</option>
        <option value="AUD">AUD ($) - Australia</option>
        <option value="GBP">GBP (£) - UK</option>
        <option value="EUR">EUR (€) - Germany</option>
      </select>
    </div>
  </header>
  <main>
    <section class="laundry-form-section">
      <h2>Book Your Laundry</h2>
      <form id="laundryForm">
        <input type="text" id="customerName" placeholder="Your Name" required>
        <input type="tel" id="phoneNumber" placeholder="Phone Number" required>
        <textarea id="itemDetails" placeholder="Describe Your Laundry Items" required></textarea>
        <input type="number" id="price" placeholder="Estimated Price" min="0" required>
        <button type="submit">Add Laundry</button>
      </form>
    </section>
    <section>
      <h2>Submitted Orders</h2>
      <ul id="ordersList"></ul>
    </section>
  </main>
  <footer>
    <p>&copy; 2025 BubbleBurst Laundry. All rights reserved.</p>
  </footer>
  <script>
    const currencySymbols = {
      USD: '$',
      AED: 'د.إ',
      SAR: 'ر.س',
      PKR: '₨',
      AUD: '$',
      GBP: '£',
      EUR: '€'
    };

    document.getElementById('laundryForm').addEventListener('submit', function(e) {
      e.preventDefault();
      const name = document.getElementById('customerName').value.trim();
      const phone = document.getElementById('phoneNumber').value.trim();
      const items = document.getElementById('itemDetails').value.trim();
      const price = document.getElementById('price').value.trim();
      const currency = document.getElementById('currency').value;

      addOrder({ name, phone, items, price, currency });

      // Reset form
      this.reset();
    });

    // Render submitted orders
    function addOrder(order) {
      const list = document.getElementById('ordersList');
      const li = document.createElement('li');
      li.className = 'order-item';
      li.innerHTML = `
        <div><strong>Name:</strong> ${order.name}</div>
        <div><strong>Phone:</strong> ${order.phone}</div>
        <div><strong>Items:</strong> ${order.items}</div>
        <div><strong>Estimated Price:</strong> <span class="currency">${currencySymbols[order.currency]}</span>${order.price} (${order.currency})</div>
      `;
      list.prepend(li);
    }

    // (Optional) You can expand on currency selection here if needed
    document.getElementById('currency').addEventListener('change', function() {
      // You can trigger updated behaviors on currency change.
    });
  </script>
</body>
</html>
