<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <title>لوحة المشرف</title>
  <style>
    body {
      font-family: 'Cairo', sans-serif;
      background: #f4f6f8;
      text-align: center;
      padding: 20px;
    }
    h1 { color: #0d47a1; }
    table {
      width: 90%;
      max-width: 800px;
      margin: 20px auto;
      border-collapse: collapse;
      background: white;
      border-radius: 10px;
      overflow: hidden;
    }
    th, td {
      padding: 12px;
      border-bottom: 1px solid #ccc;
    }
    th {
      background: #0d47a1;
      color: white;
    }
    tr:hover {
      background: #e3f2fd;
    }
    button {
      margin-top: 20px;
      background: #d32f2f;
      color: white;
      border: none;
      padding: 10px 20px;
      border-radius: 8px;
      cursor: pointer;
    }
    button:hover {
      background: #b71c1c;
    }
  </style>
</head>
<body>
  <h1>📊 لوحة المشرف</h1>
  <table>
    <thead>
      <tr>
        <th>الاسم واللقب</th>
        <th>الموضوع</th>
      </tr>
    </thead>
    <tbody id="dataBody"></tbody>
  </table>
  <button onclick="clearData()">🗑️ حذف جميع البيانات</button>

  <script>
    function loadData() {
      const data = JSON.parse(localStorage.getItem('chosenTopics') || '[]');
      const tbody = document.getElementById('dataBody');
      tbody.innerHTML = '';
      data.forEach(item => {
        const row = document.createElement('tr');
        row.innerHTML = `<td>${item.name}</td><td>${item.topic}</td>`;
        tbody.appendChild(row);
      });
    }

    function clearData() {
      if (confirm("هل أنت متأكد من حذف جميع البيانات؟")) {
        localStorage.removeItem('chosenTopics');
        loadData();
      }
    }

    loadData();
  </script>
</body>
</html>
