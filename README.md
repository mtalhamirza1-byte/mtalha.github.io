<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Laundry Manager</title>
  <style>
    /* ===== RESET ===== */
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body { font-family: Arial, sans-serif; background: #f4f7fa; color: #333; }

    /* ===== LAYOUT ===== */
    .container { display: flex; height: 100vh; overflow: hidden; }
    .sidebar {
      width: 220px; background: linear-gradient(180deg, #4facfe, #00f2fe);
      color: #fff; display: flex; flex-direction: column;
      padding: 20px 10px; transition: all 0.3s ease;
    }
    .logo {
      font-size: 22px; font-weight: bold; text-align: center;
      margin-bottom: 30px; letter-spacing: 1px;
    }
    .sidebar button {
      background: transparent; border: none; color: #fff;
      padding: 12px; margin: 5px 0; text-align: left;
      border-radius: 8px; font-size: 16px; cursor: pointer;
      transition: background 0.3s ease, transform 0.2s;
    }
    .sidebar button:hover {
      background: rgba(255,255,255,0.2); transform: translateX(5px);
    }

    .main { flex: 1; padding: 20px; overflow-y: auto; }
    h2 { margin-bottom: 15px; background: linear-gradient(90deg,#4facfe,#00f2fe);
         -webkit-background-clip: text; -webkit-text-fill-color: transparent; }

    /* ===== CARDS ===== */
    .card {
      background: #fff; border-radius: 12px; padding: 20px;
      box-shadow: 0 4px 10px rgba(0,0,0,0.1); margin-bottom: 20px;
      transition: transform 0.2s;
    }
    .card:hover { transform: scale(1.02); }

    /* ===== TABLES ===== */
    table { width: 100%; border-collapse: collapse; margin-top: 10px; }
    th, td { border: 1px solid #ddd; padding: 10px; text-align: center; }
    th { background: #4facfe; color: white; }
    tr:nth-child(even) { background: #f9f9f9; }
    tr:hover { background: #eefaff; }

    /* ===== FORMS ===== */
    form { display: flex; flex-wrap: wrap; gap: 10px; }
    input, select {
      flex: 1; padding: 10px; border: 1px solid #ccc;
      border-radius: 8px; font-size: 14px;
    }
    button.submit-btn {
      flex: 1; background: linear-gradient(90deg,#43e97b,#38f9d7);
      color: white; border: none; padding: 12px;
      border-radius: 8px; cursor: pointer; font-size: 15px;
      transition: opacity 0.3s ease;
    }
    button.submit-btn:hover { opacity: 0.8; }

    /* ===== RESPONSIVE ===== */
    @media (max-width: 768px) {
      .sidebar { width: 70px; }
      .sidebar button { font-size: 12px; padding: 10px; text-align: center; }
      .sidebar .logo { font-size: 14px; margin-bottom: 10px; }
    }
  </style>
</head>
<body>
  <div class="container">
    <!-- Sidebar -->
    <div class="sidebar">
      <div class="logo">🧺 Bubble Wash</div>
      <button onclick="showTab('dashboard')">Dashboard</button>
      <button onclick="showTab('customers')">Customers</button>
      <button onclick="showTab('orders')">Orders</button>
      <button onclick="showTab('reports')">Reports</button>
      <button onclick="showTab('settings')">Settings</button>
    </div>

    <!-- Main Content -->
    <div class="main">
      <!-- Dashboard -->
      <div id="dashboard" class="tab card">
        <h2>Dashboard</h2>
        <p>Total Customers: <span id="totalCustomers">0</span></p>
        <p>Total Orders: <span id="totalOrders">0</span></p>
        <p>Total Revenue: $<span id="totalRevenue">0</span></p>
        <p>Pending Balance: $<span id="pendingBalance">0</span></p>
      </div>

      <!-- Customers -->
      <div id="customers" class="tab card" style="display:none;">
        <h2>Customers</h2>
        <form onsubmit="addCustomer(event)">
          <input type="text" id="custName" placeholder="Name" required>
          <input type="text" id="custPhone" placeholder="Phone" required>
          <input type="text" id="custAddress" placeholder="Address" required>
          <button class="submit-btn" type="submit">Add Customer</button>
        </form>
        <table>
          <thead><tr><th>ID</th><th>Name</th><th>Phone</th><th>Address</th></tr></thead>
          <tbody id="customerTable"></tbody>
        </table>
      </div>

      <!-- Orders -->
      <div id="orders" class="tab card" style="display:none;">
        <h2>Orders</h2>
        <form onsubmit="addOrder(event)">
          <select id="orderCustomer" required></select>
          <select id="orderService" required></select>
          <input type="number" id="orderQty" placeholder="Quantity" min="1" required>
          <select id="orderStatus">
            <option value="Unpaid">Unpaid</option>
            <option value="Paid">Paid</option>
          </select>
          <button class="submit-btn" type="submit">Add Order</button>
        </form>
        <table>
          <thead><tr><th>Voucher</th><th>Customer</th><th>Service</th><th>Qty</th><th>Total</th><th>Status</th></tr></thead>
          <tbody id="orderTable"></tbody>
        </table>
      </div>

      <!-- Reports -->
      <div id="reports" class="tab card" style="display:none;">
        <h2>Reports</h2>
        <p>Weekly Revenue: $<span id="weeklyRev">0</span></p>
        <p>Monthly Revenue: $<span id="monthlyRev">0</span></p>
        <p>Yearly Revenue: $<span id="yearlyRev">0</span></p>
      </div>

      <!-- Settings -->
      <div id="settings" class="tab card" style="display:none;">
        <h2>Services</h2>
        <form onsubmit="addService(event)">
          <input type="text" id="serviceName" placeholder="Service Name" required>
          <input type="number" id="servicePrice" placeholder="Price" required>
          <button class="submit-btn" type="submit">Add Service</button>
        </form>
        <table>
          <thead><tr><th>ID</th><th>Service</th><th>Price</th></tr></thead>
          <tbody id="serviceTable"></tbody>
        </table>
      </div>
    </div>
  </div>

  <script>
    // ===== DATA ARRAYS =====
    var customers = [];
    var services = [];
    var orders = [];
    var voucherCounter = 1;

    // ===== NAVIGATION =====
    function showTab(tabId) {
      var tabs = document.getElementsByClassName('tab');
      for (var i=0;i<tabs.length;i++) { tabs[i].style.display='none'; }
      document.getElementById(tabId).style.display='block';
    }

    // ===== CUSTOMERS =====
    function addCustomer(e) {
      e.preventDefault();
      var name = document.getElementById('custName').value;
      var phone = document.getElementById('custPhone').value;
      var addr = document.getElementById('custAddress').value;
      var id = customers.length+1;
      customers.push({id:id,name:name,phone:phone,address:addr});
      document.getElementById('custName').value='';
      document.getElementById('custPhone').value='';
      document.getElementById('custAddress').value='';
      renderCustomers(); updateDashboard(); updateOrderCustomerOptions();
    }
    function renderCustomers() {
      var t=''; for(var i=0;i<customers.length;i++){
        var c=customers[i];
        t+="<tr><td>"+c.id+"</td><td>"+c.name+"</td><td>"+c.phone+"</td><td>"+c.address+"</td></tr>";
      }
      document.getElementById('customerTable').innerHTML=t;
    }

    // ===== SERVICES =====
    function addService(e){
      e.preventDefault();
      var sName=document.getElementById('serviceName').value;
      var sPrice=parseFloat(document.getElementById('servicePrice').value);
      var id=services.length+1;
      services.push({id:id,name:sName,price:sPrice});
      document.getElementById('serviceName').value='';
      document.getElementById('servicePrice').value='';
      renderServices(); updateOrderServiceOptions();
    }
    function renderServices(){
      var t=''; for(var i=0;i<services.length;i++){
        var s=services[i];
        t+="<tr><td>"+s.id+"</td><td>"+s.name+"</td><td>$"+s.price.toFixed(2)+"</td></tr>";
      }
      document.getElementById('serviceTable').innerHTML=t;
    }

    // ===== ORDERS =====
    function addOrder(e){
      e.preventDefault();
      if(customers.length==0||services.length==0){alert("Add customers and services first."); return;}
      var custId=document.getElementById('orderCustomer').value;
      var servId=document.getElementById('orderService').value;
      var qty=parseInt(document.getElementById('orderQty').value);
      var status=document.getElementById('orderStatus').value;
      var cust=customers.find(function(c){return c.id==custId;});
      var serv=services.find(function(s){return s.id==servId;});
      var total=serv.price*qty;
      orders.push({voucher:voucherCounter++,customer:cust.name,service:serv.name,qty:qty,total:total,status:status});
      document.getElementById('orderQty').value='';
      renderOrders(); updateDashboard(); updateReports();
    }
    function renderOrders(){
      var t=''; for(var i=0;i<orders.length;i++){
        var o=orders[i];
        t+="<tr><td>"+o.voucher+"</td><td>"+o.customer+"</td><td>"+o.service+"</td><td>"+o.qty+"</td><td>$"+o.total.toFixed(2)+"</td><td>"+o.status+"</td></tr>";
      }
      document.getElementById('orderTable').innerHTML=t;
    }
    function updateOrderCustomerOptions(){
      var sel=document.getElementById('orderCustomer'); sel.innerHTML='';
      for(var i=0;i<customers.length;i++){
        sel.innerHTML+="<option value='"+customers[i].id+"'>"+customers[i].name+"</option>";
      }
    }
    function updateOrderServiceOptions(){
      var sel=document.getElementById('orderService'); sel.innerHTML='';
      for(var i=0;i<services.length;i++){
        sel.innerHTML+="<option value='"+services[i].id+"'>"+services[i].name+"</option>";
      }
    }

    // ===== DASHBOARD =====
    function updateDashboard(){
      document.getElementById('totalCustomers').innerText=customers.length;
      document.getElementById('totalOrders').innerText=orders.length;
      var rev=0,pending=0;
      for(var i=0;i<orders.length;i++){
        rev+=orders[i].total;
        if(orders[i].status=="Unpaid"){pending+=orders[i].total;}
      }
      document.getElementById('totalRevenue').innerText=rev.toFixed(2);
      document.getElementById('pendingBalance').innerText=pending.toFixed(2);
    }

    // ===== REPORTS =====
    function updateReports(){
      var weekly=0,monthly=0,yearly=0;
      for(var i=0;i<orders.length;i++){
        var amt=orders[i].total;
        weekly+=amt; monthly+=amt; yearly+=amt;
      }
      document.getElementById('weeklyRev').innerText=weekly.toFixed(2);
      document.getElementById('monthlyRev').innerText=monthly.toFixed(2);
      document.getElementById('yearlyRev').innerText=yearly.toFixed(2);
    }
  </script>
</body>
</html>
