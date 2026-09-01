<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <title>مطبعة اللون الذهبي - النظام المحمي</title>
    
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-firestore.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-auth.js"></script>
    
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;700&display=swap" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined" rel="stylesheet">
    
    <style>
        * { font-family: 'Cairo', sans-serif !important; box-sizing: border-box; }
        body { background-color: #f4f7f6; margin: 0; padding-bottom: 90px; text-align: right; }
        .gold-header { background: #1a1a1a; color: #d4af37; border-bottom: 4px solid #d4af37; }
        .cust-card { background: white; border-radius: 20px; border-right: 8px solid #d4af37; box-shadow: 0 4px 12px rgba(0,0,0,0.05); margin-bottom: 12px; padding: 20px; cursor: pointer; }
        .item-box { background: #fff; border: 1px solid #e2e8f0; border-radius: 15px; padding: 15px; border-right: 5px solid #1a1a1a; margin-bottom: 12px; position: relative; }
        .btn-main { background: linear-gradient(135deg, #d4af37 0%, #735c00 100%); color: white; border-radius: 12px; font-weight: bold; }
        @media print { .no-print { display: none !important; } body { background: white; } .print-header { display: block !important; border-bottom: 2px solid #d4af37; padding-bottom: 15px; margin-bottom: 20px; } }
    </style>
</head>
<body>

<div id="app">
    <!-- شاشة تسجيل الدخول -->
    <div id="login-screen" class="flex items-center justify-center min-h-screen p-6">
        <div class="bg-white p-8 rounded-3xl shadow-2xl w-full max-w-md border-t-8 border-[#d4af37] text-center">
            <div class="w-16 h-16 bg-black rounded-2xl mx-auto mb-6 flex items-center justify-center border border-[#d4af37]">
                <span class="text-[#d4af37] font-bold text-2xl">G</span>
            </div>
            <h1 class="text-xl font-bold mb-6">دخول مطبعة اللون الذهبي</h1>
            <input type="email" id="login-email" placeholder="البريد الإلكتروني" class="w-full p-4 border rounded-xl mb-3 outline-none">
            <input type="password" id="login-pass" placeholder="كلمة المرور" class="w-full p-4 border rounded-xl mb-6 outline-none">
            <button onclick="handleLogin()" id="login-btn" class="w-full bg-black text-[#d4af37] py-4 rounded-xl font-bold shadow-lg">دخول آمن</button>
        </div>
    </div>

    <!-- المحتوى الرئيسي (مخفي حتى تسجيل الدخول) -->
    <div id="main-content" class="hidden">
        <header class="gold-header p-4 flex justify-between items-center no-print shadow-xl">
            <div class="flex items-center gap-3">
                <div class="w-10 h-10 bg-[#d4af37] rounded-lg flex items-center justify-center text-black font-bold">G</div>
                <h1 class="text-lg font-bold">مطبعة اللون الذهبي</h1>
            </div>
            <button onclick="handleLogout()" class="text-[10px] bg-red-900 text-white px-3 py-1 rounded-full font-bold">خروج</button>
        </header>

        <main class="p-4 max-w-xl mx-auto">
            <!-- شاشة الزبائن -->
            <div id="view-home">
                <div class="grid grid-cols-2 gap-3 mb-6">
                    <div class="bg-white p-4 rounded-xl shadow-sm border-b-4 border-red-500 text-center">
                        <p class="text-[10px] text-gray-400 font-bold">إجمالي المتبقي</p>
                        <p id="sum-rem" class="text-lg font-bold text-red-600">0</p>
                    </div>
                    <div class="bg-white p-4 rounded-xl shadow-sm border-b-4 border-green-500 text-center">
                        <p class="text-[10px] text-gray-400 font-bold">إجمالي المحصل</p>
                        <p id="sum-dep" class="text-lg font-bold text-green-600">0</p>
                    </div>
                </div>
                <div id="cust-list" class="space-y-3"></div>
            </div>

            <!-- شاشة تفاصيل الزبون -->
            <div id="view-detail" style="display:none">
                <button onclick="goHome()" class="mb-4 text-gray-400 font-bold flex items-center no-print">← رجوع</button>
                <div class="bg-black text-white p-6 rounded-3xl mb-4 relative shadow-xl border-r-8 border-[#d4af37]">
                    <h2 id="det-name" class="text-xl font-bold mb-1"></h2>
                    <p id="det-total" class="text-[#d4af37] text-xs font-bold"></p>
                    <button onclick="window.print()" class="absolute top-5 left-5 text-white/50 no-print">PDF</button>
                </div>
                <button onclick="addMore()" class="w-full bg-green-600 text-white py-3 rounded-xl font-bold mb-6 shadow-md no-print">+ أضف طلب جديد لهذا الزبون</button>
                <div id="items-list" class="space-y-4"></div>
            </div>

            <!-- شاشة الإضافة -->
            <div id="view-add" style="display:none">
                <h2 class="text-xl font-bold mb-5 text-[#735c00]">تسجيل شغل جديد</h2>
                <div class="bg-white p-6 rounded-3xl shadow-lg space-y-4">
                    <input type="text" id="in-c" placeholder="اسم الزبون" class="w-full p-4 border rounded-2xl outline-none">
                    <input type="text" id="in-i" placeholder="المنتج (الآيتم)" class="w-full p-4 border rounded-2xl outline-none">
                    <textarea id="in-d" rows="3" placeholder="المواصفات الفنية..." class="w-full p-4 border rounded-2xl outline-none"></textarea>
                    <div class="grid grid-cols-2 gap-4">
                        <input type="number" id="in-q" placeholder="العدد" class="p-4 border rounded-2xl">
                        <input type="number" id="in-p" placeholder="السعر الكلي" class="p-4 border rounded-2xl">
                    </div>
                    <input type="number" id="in-dep" placeholder="العربون" class="p-4 border rounded-2xl" value="0">
                    <button id="save-btn" onclick="saveData()" class="w-full bg-black text-[#d4af37] py-5 rounded-2xl text-lg font-bold">حفظ الطلب سحابياً ✅</button>
                    <button onclick="goHome()" class="w-full text-gray-400 py-2">إلغاء</button>
                </div>
            </div>
        </main>

        <nav class="fixed bottom-0 left-0 w-full bg-white border-t p-3 flex justify-around items-center shadow-2xl no-print z-50">
            <button onclick="goHome()" class="flex flex-col items-center text-[#d4af37]"><span class="material-symbols-outlined text-3xl">folder_shared</span><span class="text-[10px] font-bold">الزبائن</span></button>
            <button onclick="goAdd()" class="bg-black text-[#d4af37] w-14 h-14 rounded-full flex items-center justify-center -mt-12 border-4 border-[#f4f7f6] shadow-lg"><span class="material-symbols-outlined text-3xl">add</span></button>
            <button onclick="alert('قسم التقارير المالية تحت التطوير')" class="flex flex-col items-center text-gray-300"><span class="material-symbols-outlined text-3xl">analytics</span><span class="text-[10px] font-bold">التقارير</span></button>
        </nav>
    </div>
</div>

<script>
    const config = {
        apiKey: "AIzaSyBnfABXbY5qxnirc3sumFX4TTOtlfJEM7s",
        projectId: "golden-92eb1",
        appId: "1:682494914009:web:518e6365b0da3627affdc8"
    };

    firebase.initializeApp(config);
    const db = firebase.firestore();
    const auth = firebase.auth();
    let orders = [];
    let currentCust = "";

    // مراقبة حالة الدخول
    auth.onAuthStateChanged(user => {
        if (user) {
            document.getElementById('login-screen').classList.add('hidden');
            document.getElementById('main-content').classList.remove('hidden');
            startListening();
        } else {
            document.getElementById('login-screen').classList.remove('hidden');
            document.getElementById('main-content').classList.add('hidden');
        }
    });

    function handleLogin() {
        const email = document.getElementById('login-email').value;
        const pass = document.getElementById('login-pass').value;
        auth.signInWithEmailAndPassword(email, pass).catch(err => alert("خطأ: " + err.message));
    }

    function handleLogout() { auth.signOut(); }

    function goHome() { hide(); document.getElementById('view-home').style.display='block'; }
    function goAdd() { hide(); document.getElementById('in-c').disabled = false; document.getElementById('view-add').style.display='block'; }
    function addMore() { hide(); document.getElementById('in-c').value = currentCust; document.getElementById('in-c').disabled = true; document.getElementById('view-add').style.display='block'; }
    function goDetail(name) { hide(); currentCust = name; document.getElementById('view-detail').style.display='block'; renderDetails(name); }
    function hide() { ['view-home','view-detail','view-add'].forEach(s => document.getElementById(s).style.display='none'); }

    function startListening() {
        db.collection("orders").onSnapshot(snap => {
            orders = [];
            snap.forEach(doc => { let d = doc.data(); d.id = doc.id; orders.push(d); });
            orders.sort((a,b) => (b.createdAt?.seconds || 0) - (a.createdAt?.seconds || 0));
            renderHome();
            if(document.getElementById('view-detail').style.display === 'block') renderDetails(currentCust);
        });
    }

    function renderHome() {
        const list = document.getElementById('cust-list');
        const names = [...new Set(orders.map(o => o.customer))].filter(n => n);
        let tRem = 0, tDep = 0;
        list.innerHTML = names.length ? "" : '<p class="text-center py-20 text-gray-400">لا يوجد زبائن حالياً</p>';
        names.forEach(n => {
            const myO = orders.filter(o => o.customer === n);
            let r = 0, d = 0;
            myO.forEach(o => { r += (o.remaining || 0); d += (o.deposit || 0); });
            tRem += r; tDep += d;
            const div = document.createElement('div');
            div.className = "cust-card flex justify-between items-center";
            div.onclick = () => goDetail(n);
            div.innerHTML = `<div><h3 class="font-bold text-lg">${n}</h3><p class="text-[10px] text-gray-400 font-bold">${myO.length} طلبات</p></div><div class="text-red-600 font-bold text-sm">${r.toLocaleString()} ر.س</div>`;
            list.appendChild(div);
        });
        document.getElementById('sum-rem').innerText = tRem.toLocaleString();
        document.getElementById('sum-dep').innerText = tDep.toLocaleString();
    }

    function renderDetails(name) {
        document.getElementById('det-name').innerText = name;
        const myO = orders.filter(o => o.customer === name);
        const rem = myO.reduce((s, o) => s + (o.remaining || 0), 0);
        document.getElementById('det-total').innerText = "إجمالي المتبقي: " + rem.toLocaleString();
        const list = document.getElementById('items-list');
        list.innerHTML = "";
        myO.forEach(o => {
            const div = document.createElement('div');
            div.className = "item-box shadow-sm";
            const dt = o.createdAt ? new Date(o.createdAt.seconds * 1000).toLocaleDateString('ar-EG') : 'الآن';
            div.innerHTML = `<div class="flex justify-between mb-2"><h4 class="font-bold text-[#735c00] text-lg">${o.itemName}</h4><span class="text-[10px] font-bold underline">${o.status || 'تنفيذ'}</span></div><p class="text-[11px] text-gray-500 mb-3 bg-gray-50 p-2 rounded">${o.details || '-'}</p><div class="grid grid-cols-4 gap-1 text-center text-[10px] border-t pt-2 font-bold"><div><p class="text-gray-400 uppercase">العدد</p>${o.quantity}</div><div><p class="text-gray-400 uppercase">السعر</p>${o.totalPrice}</div><div><p class="text-gray-400 uppercase text-green-600">المدفوع</p>${o.deposit}</div><div class="text-red-600 font-bold">باقي</p>${o.remaining}</div></div><p class="text-[8px] text-gray-300 mt-2">${dt}</p><div class="mt-4 no-print flex gap-2"><button onclick="upPay('${o.id}',${o.totalPrice},${o.deposit})" class="flex-1 bg-black text-[#d4af37] py-2 rounded-xl text-xs font-bold shadow-md">قبض مبلغ</button><button onclick="delItem('${o.id}')" class="text-red-200"><span class="material-symbols-outlined text-sm">delete</span></button></div>`;
            list.appendChild(div);
        });
    }

    async function saveData() {
        const b = document.getElementById('save-btn');
        const c = document.getElementById('in-c').value;
        const i = document.getElementById('in-i').value;
        const p = parseFloat(document.getElementById('in-p').value) || 0;
        const d = parseFloat(document.getElementById('in-dep').value) || 0;
        if(!c || !i || !p) return alert("أكمل البيانات");
        b.disabled = true; b.innerText = "جاري الحفظ بالسحاب...";
        await db.collection("orders").add({
            customer: c, itemName: i, details: document.getElementById('in-d').value,
            quantity: document.getElementById('in-q').value || 0,
            totalPrice: p, deposit: d, remaining: p - d, status: "قيد التنفيذ",
            createdAt: firebase.firestore.FieldValue.serverTimestamp()
        });
        alert("تم الحفظ!"); b.disabled = false; b.innerText = "حفظ الآن ✅"; goDetail(c);
    }

    async function upPay(id, t, d) {
        const a = parseFloat(prompt("أدخل مبلغ الدفعة:"));
        if(!isNaN(a)) await db.collection("orders").doc(id).update({deposit: d+a, remaining: t-(d+a)});
    }

    async function delItem(id) { if(confirm("حذف؟")) await db.collection("orders").doc(id).delete(); }
</script>
</body>
</html>
