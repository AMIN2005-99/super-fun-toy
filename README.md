<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>اختيار المواضيع</title>
  <style>
    body {
      font-family: "Cairo", sans-serif;
      background: linear-gradient(135deg, #2193b0, #6dd5ed);
      color: #222;
      margin: 0;
      padding: 0;
      min-height: 100vh;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: flex-start;
    }

    h1 {
      margin-top: 30px;
      color: #fff;
      text-shadow: 1px 1px 4px #000;
    }

    .container {
      background: #fff;
      padding: 20px;
      border-radius: 15px;
      margin: 20px;
      width: 90%;
      max-width: 900px;
      box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
      transition: all 0.3s ease;
    }

    label {
      font-weight: bold;
    }

    input, button {
      width: 100%;
      padding: 10px;
      margin: 10px 0;
      border-radius: 10px;
      border: 1px solid #ccc;
      font-size: 1em;
    }

    button {
      background: #2193b0;
      color: white;
      cursor: pointer;
      transition: background 0.3s;
    }

    button:hover {
      background: #17657a;
    }

    .topic-list {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
      gap: 10px;
      margin-top: 20px;
    }

    .topic-card {
      background: #f5f5f5;
      padding: 12px;
      border-radius: 10px;
      cursor: pointer;
      transition: all 0.2s;
      border: 2px solid transparent;
    }

    .topic-card:hover {
      background: #e0f7fa;
      transform: scale(1.02);
    }

    .selected {
      border-color: #2193b0;
      background: #b2ebf2;
    }

    @media (max-width: 768px) {
      .topic-list {
        grid-template-columns: 1fr;
      }
    }

    .admin-table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 20px;
    }

    .admin-table th, .admin-table td {
      border: 1px solid #ccc;
      padding: 10px;
      text-align: center;
    }

    .admin-table th {
      background: #2193b0;
      color: white;
    }

    .action-btn {
      padding: 5px 10px;
      border-radius: 5px;
      border: none;
      cursor: pointer;
      font-size: 0.9em;
    }

    .edit-btn {
      background: #ffc107;
      color: #000;
    }

    .delete-btn {
      background: #dc3545;
      color: white;
    }

    .message {
      background: #28a745;
      color: white;
      padding: 10px;
      border-radius: 10px;
      margin-top: 10px;
      display: none;
    }
  </style>
</head>
<body>

  <h1>📘 اختيار الموضوع</h1>

  <div class="container" id="studentPage">
    <label>الاسم واللقب:</label>
    <input type="text" id="studentName" placeholder="اكتب اسمك الكامل" />

    <label>اختر الموضوع:</label>
    <input type="text" id="selectedTopic" readonly placeholder="اختر موضوعًا من القائمة أدناه" />

    <div class="topic-list" id="topicList"></div>

    <button id="saveBtn">💾 حفظ الاختيار</button>
    <p class="message" id="successMsg">✅ تم حفظ اختيارك بنجاح!</p>

    <button onclick="goToAdmin()">🔒 صفحة المشرف</button>
  </div>

  <!-- صفحة المشرف -->
  <div class="container" id="adminPage" style="display:none;">
    <h2>📋 قائمة الطلبة والمواضيع المختارة</h2>
    <table class="admin-table">
      <thead>
        <tr>
          <th>الاسم واللقب</th>
          <th>الموضوع</th>
          <th>العمليات</th>
        </tr>
      </thead>
      <tbody id="adminTableBody"></tbody>
    </table>
    <button onclick="logoutAdmin()">⬅️ تسجيل الخروج</button>
  </div>

  <script>
    const topics = [
      "الذات والوعي",
      "رحلتك الخاصة: كيف تكتشف نفسك قبل أن تختار طريقك؟",
      "من الفوضى إلى التركيز: فن إدارة انتباهك في زمن التشويش",
      "الذكاء العاطفي… كيف تفهم قلبك قبل أن تحاول فهم الآخرين؟",
      "القيم الشخصية: البوصلة الخفية التي تقود قراراتك",
      "بين الشغف والواقعية: كيف تختار مسارك دون أن تندم؟",
      "فن الهدوء الذكي: كيف تكون ثابتًا في عالم متسارع؟",
      "لغة الجسد الخفية: كيف “تتحدث” دون أن تنطق؟",
      "التأثير بدون سلطة: كيف تقود الآخرين بأسلوبك فقط؟",
      "كيف تبدأ محادثة تغيّر حياتك؟",
      "الكاريزما… مهارة أم طبيعة؟ كيف تبني حضورك بثقة؟",
      "الذكاء الاجتماعي في العلاقات الجامعية والمهنية",
      "فن الإقناع: كيف تزرع فكرة في ذهن شخص آخر؟",
      "كيف تولد الأفكار العظيمة؟ (تقنيات التفكير الإبداعي)",
      "من فكرة إلى تأثير: كيف تحوّل حلمك إلى مشروع يخدم الآخرين؟",
      "الريادة الهادفة: أن تربح وتُحدث فرقًا في الوقت نفسه",
      "التجارة الإلكترونية والذكاء الاصطناعي: فرص جيل جديد",
      "العمل الحر: الحرية كخيار مهني حقيقي",
      "الذكاء الاصطناعي والإنسان: من يخدم من؟",
      "كيف تتعامل مع الفشل دون أن تفقد شغفك؟",
      "فن المثابرة: كيف تستمر حين يتوقف الآخرون؟",
      "كيف تتغلب على التسويف دون أن تعتمد على التحفيز؟",
      "العادات الصغيرة تصنع العظمة: قانون 1٪ يوميًا",
      "مواجهة الخوف من التحدث أمام الناس: خطوات عملية",
      "الطالب القائد: كيف تصنع تأثيرًا حقيقيًا داخل الجامعة؟",
      "بين لغتين: كيف تحافظ على هويتك وتتقن لغة العالم؟",
      "الرسول ﷺ كقدوة قيادية وإنسانية في العصر الحديث",
      "القراءة كرحلة وليس هواية: كيف تقرأ لتتغيّر؟",
      "قوة التطوع: أن تكون نافعًا أكثر من أن تكون مشهورًا",
      "جيل المعرفة: كيف تتعلم ذاتيًا وتصبح خبيرًا بدون شهادة؟",
      "من الجامعة إلى العالم: كيف تبدأ قصتك الآن؟"
    ];

    const topicList = document.getElementById('topicList');
    const selectedTopicInput = document.getElementById('selectedTopic');
    const successMsg = document.getElementById('successMsg');

    topics.forEach(topic => {
      const div = document.createElement('div');
      div.textContent = topic;
      div.className = 'topic-card';
      div.onclick = () => selectTopic(div, topic);
      topicList.appendChild(div);
    });

    function selectTopic(div, topic) {
      document.querySelectorAll('.topic-card').forEach(c => c.classList.remove('selected'));
      div.classList.add('selected');
      selectedTopicInput.value = topic;
    }

    document.getElementById('saveBtn').onclick = () => {
      const name = document.getElementById('studentName').value.trim();
      const topic = selectedTopicInput.value.trim();
      if (!name || !topic) {
        alert("الرجاء إدخال الاسم واختيار الموضوع");
        return;
      }

      let data = JSON.parse(localStorage.getItem('students') || "[]");

      if (data.some(d => d.topic === topic)) {
        alert("هذا الموضوع تم اختياره بالفعل من قبل طالب آخر!");
        return;
      }

      data.push({ name, topic });
      localStorage.setItem('students', JSON.stringify(data));

      successMsg.style.display = "block";
      setTimeout(() => successMsg.style.display = "none", 3000);

      document.getElementById('studentName').value = "";
      selectedTopicInput.value = "";
      document.querySelectorAll('.topic-card').forEach(c => c.classList.remove('selected'));
    };

    function goToAdmin() {
      const pass = prompt("أدخل كلمة مرور المشرف:");
      if (pass === "admin123") {
        document.getElementById('studentPage').style.display = "none";
        document.getElementById('adminPage').style.display = "block";
        loadAdminTable();
      } else {
        alert("كلمة المرور غير صحيحة");
      }
    }

    function logoutAdmin() {
      document.getElementById('adminPage').style.display = "none";
      document.getElementById('studentPage').style.display = "block";
    }

    function loadAdminTable() {
      const data = JSON.parse(localStorage.getItem('students') || "[]");
      const tbody = document.getElementById('adminTableBody');
      tbody.innerHTML = "";
      data.forEach((d, i) => {
        const tr = document.createElement('tr');
        tr.innerHTML = `
          <td>${d.name}</td>
          <td>${d.topic}</td>
          <td>
            <button class="action-btn edit-btn" onclick="editStudent(${i})">✏️ تعديل</button>
            <button class="action-btn delete-btn" onclick="deleteStudent(${i})">🗑️ حذف</button>
          </td>
        `;
        tbody.appendChild(tr);
      });
    }

    function deleteStudent(index) {
      if (confirm("هل أنت متأكد من حذف هذا الطالب؟")) {
        let data = JSON.parse(localStorage.getItem('students') || "[]");
        data.splice(index, 1);
        localStorage.setItem('students', JSON.stringify(data));
        loadAdminTable();
      }
    }

    function editStudent(index) {
      let data = JSON.parse(localStorage.getItem('students') || "[]");
      const newName = prompt("الاسم الجديد:", data[index].name);
      if (newName) {
        data[index].name = newName;
        localStorage.setItem('students', JSON.stringify(data));
        loadAdminTable();
      }
    }
  </script>
</body>
</html>
