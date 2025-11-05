<index.html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Laundry Manager</title>
  <style>
    /* ===== RESET ===== */
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body { font-family: Arial, sans-serif; background: #f4f7fa; color: #333; }

  
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
<!-- Your HTML code here -->
<div class="card">
  <h2>Currency Selector</h2>
  <label for="currency">Choose Currency:</label>
  <select id="currency">
    <option value="USD">USD</option>
    <option value="PKR">PKR</option>
    <option value="EUR">EUR</option>
    <option value="GBP">GBP</option>
  </select>
  <p>Price: <span class="price" data-usd="5">5 USD</span></p>
</div>

<div class="card">
  <h2>Customer List</h2>
  <table id="customerTable">
    <thead>
      <tr>
        <th>Name</th><th>Phone</th><th>Email</th><th>Address</th><th>Notes</th>
      </tr>
    </thead>
    <tbody></tbody>
  </table>
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
