# Taktokly<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>توكتوكلي - خدمة التوصيل المحلية</title>
    <!-- Firebase SDK -->
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <style>
        :root {
            --primary-admin: #8e44ad;
            --primary-client: #3498db;
            --primary-driver: #2ecc71;
            --light-bg: #f9f9f9;
            --dark-text: #333;
            --light-text: #fff;
            --shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
            --border-radius: 8px;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background-color: var(--light-bg);
            color: var(--dark-text);
            line-height: 1.6;
            padding: 0;
            margin: 0;
            min-height: 100vh;
        }

        .container {
            max-width: 100%;
            padding: 20px;
            margin: 0 auto;
        }

        header {
            text-align: center;
            padding: 20px 0;
            background: linear-gradient(135deg, var(--primary-admin), var(--primary-client), var(--primary-driver));
            color: var(--light-text);
            border-bottom-left-radius: 20px;
            border-bottom-right-radius: 20px;
            margin-bottom: 20px;
            box-shadow: var(--shadow);
        }

        .logo {
            font-size: 2.5rem;
            font-weight: bold;
            margin-bottom: 10px;
        }

        .section {
            display: none;
            background: white;
            padding: 20px;
            border-radius: var(--border-radius);
            box-shadow: var(--shadow);
            margin-bottom: 20px;
        }

        .active {
            display: block;
        }

        .btn {
            display: inline-block;
            padding: 12px 24px;
            background-color: #333;
            color: white;
            border: none;
            border-radius: var(--border-radius);
            cursor: pointer;
            font-size: 16px;
            margin: 5px;
            transition: all 0.3s ease;
        }

        .btn-admin {
            background-color: var(--primary-admin);
        }

        .btn-client {
            background-color: var(--primary-client);
        }

        .btn-driver {
            background-color: var(--primary-driver);
        }

        .btn-back {
            background-color: #7f8c8d;
        }

        .btn:hover {
            opacity: 0.9;
            transform: translateY(-2px);
        }

        .user-type-selector {
            display: flex;
            flex-direction: column;
            gap: 15px;
            margin-top: 30px;
        }

        .user-type-btn {
            padding: 20px;
            font-size: 20px;
            border-radius: var(--border-radius);
            text-align: center;
        }

        form {
            display: flex;
            flex-direction: column;
            gap: 15px;
            margin-top: 20px;
        }

        input, select {
            padding: 12px;
            border: 1px solid #ddd;
            border-radius: var(--border-radius);
            font-size: 16px;
        }

        .form-group {
            display: flex;
            flex-direction: column;
            gap: 5px;
        }

        .price-list {
            margin-top: 20px;
        }

        .price-item {
            display: flex;
            justify-content: space-between;
            padding: 10px;
            border-bottom: 1px solid #eee;
        }

        .stats-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
            gap: 15px;
            margin-top: 20px;
        }

        .stat-card {
            background: white;
            padding: 15px;
            border-radius: var(--border-radius);
            box-shadow: var(--shadow);
            text-align: center;
        }

        .request-card {
            background: white;
            padding: 15px;
            border-radius: var(--border-radius);
            box-shadow: var(--shadow);
            margin: 10px 0;
        }

        .chat-container {
            margin-top: 20px;
            border: 1px solid #ddd;
            border-radius: var(--border-radius);
            padding: 10px;
            height: 300px;
            overflow-y: auto;
        }

        .message {
            padding: 8px 12px;
            margin: 5px 0;
            border-radius: 20px;
            max-width: 80%;
        }

        .message.client {
            background-color: var(--primary-client);
            color: white;
            margin-left: auto;
        }

        .message.driver {
            background-color: var(--primary-driver);
            color: white;
            margin-right: auto;
        }

        .trip-info {
            background-color: #f8f9fa;
            padding: 15px;
            border-radius: var(--border-radius);
            margin: 15px 0;
        }

        @media (min-width: 768px) {
            .container {
                max-width: 600px;
            }
            
            .user-type-selector {
                flex-direction: row;
                justify-content: center;
            }
            
            .user-type-btn {
                flex: 1;
                max-width: 200px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <div class="logo">توكتوكلي</div>
            <p>خدمة التوصيل المحلية</p>
        </header>

        <!-- الشاشة الرئيسية -->
        <div id="main-screen" class="section active">
            <h2>اختر نوع المستخدم</h2>
            <div class="user-type-selector">
                <button class="btn user-type-btn btn-admin" onclick="showSection('admin-login')">الإدارة</button>
                <button class="btn user-type-btn btn-client" onclick="showSection('client-login')">عميل</button>
                <button class="btn user-type-btn btn-driver" onclick="showSection('driver-login')">سائق</button>
            </div>
        </div>

        <!-- قسم الإدارة -->
        <div id="admin-login" class="section">
            <h2>تسجيل دخول الإدارة</h2>
            <form id="admin-login-form">
                <div class="form-group">
                    <label for="admin-username">اسم المستخدم:</label>
                    <input type="text" id="admin-username" required>
                </div>
                <div class="form-group">
                    <label for="admin-password">كلمة المرور:</label>
                    <input type="password" id="admin-password" required>
                </div>
                <button type="submit" class="btn btn-admin">تسجيل الدخول</button>
            </form>
            <button class="btn btn-back" onclick="showSection('main-screen')">رجوع</button>
        </div>

        <div id="admin-dashboard" class="section">
            <h2>لوحة تحكم الإدارة</h2>
            <div class="admin-actions">
                <button class="btn btn-admin" onclick="showAdminTab('manage-prices')">إدارة الأسعار</button>
                <button class="btn btn-admin" onclick="showAdminTab('manage-drivers')">إدارة السائقين</button>
                <button class="btn btn-admin" onclick="showAdminTab('stats')">الإحصائيات</button>
                <button class="btn btn-admin" onclick="showAdminTab('wallet')">إعدادات المحفظة</button>
            </div>

            <div id="manage-prices" class="admin-tab">
                <h3>إدارة أسعار الرحلات</h3>
                <form id="add-price-form">
                    <div class="form-group">
                        <label for="from-location">من:</label>
                        <select id="from-location" required>
                            <option value="">اختر المكان</option>
                            <option value="أبوغيات">أبوغيات</option>
                            <option value="الزعاقة">الزعاقة</option>
                            <option value="كفر صقر">كفر صقر</option>
                            <!-- سيتم إضافة باقي الأماكن ديناميكيًا -->
                        </select>
                    </div>
                    <div class="form-group">
                        <label for="to-location">إلى:</label>
                        <select id="to-location" required>
                            <option value="">اختر المكان</option>
                            <option value="أبوغيات">أبوغيات</option>
                            <option value="الزعاقة">الزعاقة</option>
                            <option value="كفر صقر">كفر صقر</option>
                            <!-- سيتم إضافة باقي الأماكن ديناميكيًا -->
                        </select>
                    </div>
                    <div class="form-group">
                        <label for="price-value">السعر (جنيه):</label>
                        <input type="number" id="price-value" required>
                    </div>
                    <button type="submit" class="btn btn-admin">إضافة/تعديل السعر</button>
                </form>
                <div class="price-list">
                    <h4>قائمة الأسعار الحالية</h4>
                    <div id="prices-container"></div>
                </div>
            </div>

            <div id="manage-drivers" class="admin-tab" style="display:none;">
                <h3>إدارة السائقين</h3>
                <form id="add-driver-form">
                    <div class="form-group">
                        <label for="driver-name">اسم السائق:</label>
                        <input type="text" id="driver-name" required>
                    </div>
                    <div class="form-group">
                        <label for="driver-phone">رقم الهاتف:</label>
                        <input type="tel" id="driver-phone" required>
                    </div>
                    <div class="form-group">
                        <label for="driver-password">كلمة المرور:</label>
                        <input type="password" id="driver-password" required>
                    </div>
                    <button type="submit" class="btn btn-admin">إضافة سائق</button>
                </form>
                <div class="drivers-list">
                    <h4>قائمة السائقين</h4>
                    <div id="drivers-container"></div>
                </div>
            </div>

            <div id="stats" class="admin-tab" style="display:none;">
                <h3>إحصائيات الرحلات</h3>
                <div class="stats-grid" id="stats-container"></div>
            </div>

            <div id="wallet" class="admin-tab" style="display:none;">
                <h3>إعدادات المحفظة</h3>
                <form id="wallet-form">
                    <div class="form-group">
                        <label for="wallet-number">رقم المحفظة:</label>
                        <input type="text" id="wallet-number" required>
                    </div>
                    <button type="submit" class="btn btn-admin">حفظ</button>
                </form>
            </div>

            <button class="btn btn-back" onclick="adminLogout()">تسجيل الخروج</button>
        </div>

        <!-- قسم العميل -->
        <div id="client-login" class="section">
            <h2>تسجيل دخول العميل</h2>
            <form id="client-login-form">
                <div class="form-group">
                    <label for="client-phone">رقم الهاتف:</label>
                    <input type="tel" id="client-phone" placeholder="01xxxxxxxxx" required>
                </div>
                <button type="submit" class="btn btn-client">الدخول</button>
            </form>
            <button class="btn btn-back" onclick="showSection('main-screen')">رجوع</button>
        </div>

        <div id="client-dashboard" class="section">
            <h2>لوحة العميل</h2>
            <div class="trip-form">
                <h3>طلب رحلة جديدة</h3>
                <form id="request-trip-form">
                    <div class="form-group">
                        <label for="client-from">من:</label>
                        <select id="client-from" required>
                            <option value="">اختر مكان الانطلاق</option>
                            <!-- سيتم تعبئة الخيارات من Firebase -->
                        </select>
                    </div>
                    <div class="form-group">
                        <label for="client-to">إلى:</label>
                        <select id="client-to" required>
                            <option value="">اختر مكان الوصول</option>
                            <!-- سيتم تعبئة الخيارات من Firebase -->
                        </select>
                    </div>
                    <div class="form-group">
                        <label for="payment-method">طريقة الدفع:</label>
                        <select id="payment-method" required>
                            <option value="نقدا">نقداً</option>
                            <option value="محفظة">تحويل للمحفظة</option>
                        </select>
                    </div>
                    <div class="trip-info">
                        <p>السعر المقدر: <span id="trip-price">0</span> جنيه</p>
                    </div>
                    <button type="submit" class="btn btn-client">طلب الرحلة</button>
                </form>
            </div>

            <div id="active-trip" style="display:none;">
                <h3>رحلة قيد التقدم</h3>
                <div class="trip-info">
                    <p>من: <span id="current-from"></span></p>
                    <p>إلى: <span id="current-to"></span></p>
                    <p>السعر: <span id="current-price"></span> جنيه</p>
                    <p>حالة الرحلة: <span id="trip-status">بانتظار القبول</span></p>
                    <p>السائق: <span id="driver-name"></span></p>
                </div>
                
                <div class="chat-container" id="chat-messages"></div>
                
                <div class="message-input">
                    <input type="text" id="client-message" placeholder="اكتب رسالتك هنا...">
                    <button class="btn btn-client" onclick="sendMessage()">إرسال</button>
                </div>
                
                <button class="btn btn-client" id="end-trip-btn" onclick="endTrip()" disabled>إنهاء الرحلة</button>
            </div>

            <button class="btn btn-back" onclick="clientLogout()">تسجيل الخروج</button>
        </div>

        <!-- قسم السائق -->
        <div id="driver-login" class="section">
            <h2>تسجيل دخول السائق</h2>
            <form id="driver-login-form">
                <div class="form-group">
                    <label for="driver-login-phone">رقم الهاتف:</label>
                    <input type="tel" id="driver-login-phone" required>
                </div>
                <div class="form-group">
                    <label for="driver-login-password">كلمة المرور:</label>
                    <input type="password" id="driver-login-password" required>
                </div>
                <button type="submit" class="btn btn-driver">تسجيل الدخول</button>
            </form>
            <button class="btn btn-back" onclick="showSection('main-screen')">رجوع</button>
        </div>

        <div id="driver-dashboard" class="section">
            <h2>لوحة السائق</h2>
            
            <div id="driver-waiting">
                <h3>في انتظار طلبات الرحلات</h3>
                <div id="trip-requests"></div>
            </div>
            
            <div id="active-driver-trip" style="display:none;">
                <h3>رحلة حالية</h3>
                <div class="trip-info">
                    <p>من: <span id="driver-current-from"></span></p>
                    <p>إلى: <span id="driver-current-to"></span></p>
                    <p>السعر: <span id="driver-current-price"></span> جنيه</p>
                    <p>طريقة الدفع: <span id="payment-type"></span></p>
                </div>
                
                <div class="chat-container" id="driver-chat-messages"></div>
                
                <div class="message-input">
                    <input type="text" id="driver-message" placeholder="اكتب رسالتك هنا...">
                    <button class="btn btn-driver" onclick="sendDriverMessage()">إرسال</button>
                </div>
                
                <button class="btn btn-driver" id="driver-end-trip-btn" onclick="driverEndTrip()" disabled>إنهاء الرحلة</button>
            </div>
            
            <div class="driver-stats">
                <h3>إحصائياتك</h3>
                <div class="stats-grid">
                    <div class="stat-card">
                        <h4>عدد الرحلات</h4>
                        <p id="trips-count">0</p>
                    </div>
                    <div class="stat-card">
                        <h4>إجمالي الأرباح</h4>
                        <p id="total-earnings">0 جنيه</p>
                    </div>
                </div>
            </div>

            <button class="btn btn-back" onclick="driverLogout()">تسجيل الخروج</button>
        </div>
    </div>

    <script>
        // تهيئة Firebase
        const firebaseConfig = {
            apiKey: "AIzaSyEXAMPLEEXAMPLEEXAMPLEEXAMPLE",
            authDomain: "tuktukly-example.firebaseapp.com",
            projectId: "tuktukly-example",
            storageBucket: "tuktukly-example.appspot.com",
            messagingSenderId: "123456789",
            appId: "1:123456789:web:example123456789"
        };
        
        // تهيئة Firebase
        firebase.initializeApp(firebaseConfig);
        const auth = firebase.auth();
        const db = firebase.firestore();

        // متغيرات التطبيق
        let currentUser = null;
        let currentTrip = null;
        let unsubscribeTripListener = null;

        // وظائف التنقل بين الشاشات
        function showSection(sectionId) {
            document.querySelectorAll('.section').forEach(section => {
                section.classList.remove('active');
            });
            document.getElementById(sectionId).classList.add('active');
        }

        function showAdminTab(tabId) {
            document.querySelectorAll('.admin-tab').forEach(tab => {
                tab.style.display = 'none';
            });
            document.getElementById(tabId).style.display = 'block';
        }

        // تسجيل دخول الإدارة
        document.getElementById('admin-login-form').addEventListener('submit', async (e) => {
            e.preventDefault();
            const username = document.getElementById('admin-username').value;
            const password = document.getElementById('admin-password').value;
            
            // في تطبيق حقيقي، يجب التحقق من بيانات الدخول عبر Firebase Auth أو Firestore
            try {
                // هذا مثال مبسط للتوثيق، في التطبيق الحقيقي يجب استخدام Firebase Auth بشكل صحيح
                const adminsRef = db.collection('admins');
                const snapshot = await adminsRef.where('username', '==', username).get();
                
                if (snapshot.empty) {
                    alert('اسم المستخدم أو كلمة المرور غير صحيحة');
                    return;
                }
                
                let adminData;
                snapshot.forEach(doc => {
                    adminData = doc.data();
                });
                
                if (adminData.password === password) {
                    currentUser = { uid: 'admin', type: 'admin', ...adminData };
                    showSection('admin-dashboard');
                    loadAdminData();
                } else {
                    alert('اسم المستخدم أو كلمة المرور غير صحيحة');
                }
            } catch (error) {
                console.error('خطأ في تسجيل الدخول:', error);
                alert('حدث خطأ أثناء تسجيل الدخول');
            }
        });

        // تسجيل دخول العميل
        document.getElementById('client-login-form').addEventListener('submit', async (e) => {
            e.preventDefault();
            const phone = document.getElementById('client-phone').value;
            
            // التحقق من رقم الهاتف المصري
            if (!phone.match(/^01[0-2,5]{1}[0-9]{8}$/)) {
                alert('يرجى إدخال رقم هاتف مصري صحيح (10 أرقام تبدأ بـ 01)');
                return;
            }
            
            try {
                // البحث عن العميل في قاعدة البيانات أو إنشاء حساب جديد
                const clientsRef = db.collection('clients');
                const snapshot = await clientsRef.where('phone', '==', phone).get();
                
                if (snapshot.empty) {
                    // إنشاء عميل جديد
                    const newClientRef = await clientsRef.add({
                        phone: phone,
                        createdAt: firebase.firestore.FieldValue.serverTimestamp()
                    });
                    
                    currentUser = { 
                        uid: newClientRef.id, 
                        type: 'client', 
                        phone: phone 
                    };
                } else {
                    // استخدام العميل الموجود
                    snapshot.forEach(doc => {
                        currentUser = { 
                            uid: doc.id, 
                            type: 'client', 
                            ...doc.data() 
                        };
                    });
                }
                
                showSection('client-dashboard');
                loadClientData();
            } catch (error) {
                console.error('خطأ في تسجيل الدخول:', error);
                alert('حدث خطأ أثناء تسجيل الدخول');
            }
        });

        // تسجيل دخول السائق
        document.getElementById('driver-login-form').addEventListener('submit', async (e) => {
            e.preventDefault();
            const phone = document.getElementById('driver-login-phone').value;
            const password = document.getElementById('driver-login-password').value;
            
            try {
                const driversRef = db.collection('drivers');
                const snapshot = await driversRef.where('phone', '==', phone).get();
                
                if (snapshot.empty) {
                    alert('رقم الهاتف أو كلمة المرور غير صحيحة');
                    return;
                }
                
                let driverData;
                snapshot.forEach(doc => {
                    driverData = { id: doc.id, ...doc.data() };
                });
                
                if (driverData.password === password) {
                    currentUser = { 
                        uid: driverData.id, 
                        type: 'driver', 
                        ...driverData 
                    };
                    showSection('driver-dashboard');
                    loadDriverData();
                } else {
                    alert('رقم الهاتف أو كلمة المرور غير صحيحة');
                }
            } catch (error) {
                console.error('خطأ في تسجيل الدخول:', error);
                alert('حدث خطأ أثناء تسجيل الدخول');
            }
        });

        // وظائف الإدارة
        async function loadAdminData() {
            // تحميل قائمة الأسعار
            const pricesSnapshot = await db.collection('prices').get();
            const pricesContainer = document.getElementById('prices-container');
            pricesContainer.innerHTML = '';
            
            pricesSnapshot.forEach(doc => {
                const price = doc.data();
                const priceElement = document.createElement('div');
                priceElement.className = 'price-item';
                priceElement.innerHTML = `
                    <span>${price.from} → ${price.to}</span>
                    <span>${price.amount} جنيه</span>
                    <button class="btn" onclick="editPrice('${doc.id}', '${price.from}', '${price.to}', ${price.amount})">تعديل</button>
                    <button class="btn" onclick="deletePrice('${doc.id}')">حذف</button>
                `;
                pricesContainer.appendChild(priceElement);
            });
            
            // تحميل قائمة السائقين
            const driversSnapshot = await db.collection('drivers').get();
            const driversContainer = document.getElementById('drivers-container');
            driversContainer.innerHTML = '';
            
            driversSnapshot.forEach(doc => {
                const driver = doc.data();
                const driverElement = document.createElement('div');
                driverElement.className = 'price-item';
                driverElement.innerHTML = `
                    <span>${driver.name} - ${driver.phone}</span>
                    <button class="btn" onclick="editDriver('${doc.id}', '${driver.name}', '${driver.phone}')">تعديل</button>
                    <button class="btn" onclick="deleteDriver('${doc.id}')">حذف</button>
                `;
                driversContainer.appendChild(driverElement);
            });
            
            // تحميل الإحصائيات
            loadStats();
        }

        async function loadStats() {
            // هذا مثال مبسط للإحصائيات
            const statsContainer = document.getElementById('stats-container');
            statsContainer.innerHTML = `
                <div class="stat-card">
                    <h4>إجمالي الرحلات</h4>
                    <p>120</p>
                </div>
                <div class="stat-card">
                    <h4>إجمالي الأرباح</h4>
                    <p>2400 جنيه</p>
                </div>
                <div class="stat-card">
                    <h4>حصة التطبيق</h4>
                    <p>480 جنيه</p>
                </div>
                <div class="stat-card">
                    <h4>حصة السائقين</h4>
                    <p>1920 جنيه</p>
                </div>
            `;
        }

        // إضافة سعر جديد
        document.getElementById('add-price-form').addEventListener('submit', async (e) => {
            e.preventDefault();
            const from = document.getElementById('from-location').value;
            const to = document.getElementById('to-location').value;
            const amount = parseInt(document.getElementById('price-value').value);
            
            if (from === to) {
                alert('مكان الانطلاق والوصول لا يمكن أن يكونا متشابهين');
                return;
            }
            
            try {
                // التحقق إذا كان السعر موجوداً مسبقاً
                const priceSnapshot = await db.collection('prices')
                    .where('from', '==', from)
                    .where('to', '==', to)
                    .get();
                
                if (!priceSnapshot.empty) {
                    // تحديث السعر الموجود
                    priceSnapshot.forEach(async doc => {
                        await db.collection('prices').doc(doc.id).update({ amount });
                    });
                    alert('تم تحديث السعر بنجاح');
                } else {
                    // إضافة سعر جديد
                    await db.collection('prices').add({ from, to, amount });
                    alert('تم إضافة السعر بنجاح');
                }
                
                // إعادة تحميل البيانات
                loadAdminData();
            } catch (error) {
                console.error('خطأ في إضافة السعر:', error);
                alert('حدث خطأ أثناء إضافة السعر');
            }
        });

        // إضافة سائق جديد
        document.getElementById('add-driver-form').addEventListener('submit', async (e) => {
            e.preventDefault();
            const name = document.getElementById('driver-name').value;
            const phone = document.getElementById('driver-phone').value;
            const password = document.getElementById('driver-password').value;
            
            try {
                // التحقق إذا كان السائق موجوداً مسبقاً
                const driverSnapshot = await db.collection('drivers')
                    .where('phone', '==', phone)
                    .get();
                
                if (!driverSnapshot.empty) {
                    alert('رقم الهاتف مسجل مسبقاً');
                    return;
                }
                
                // إضافة سائق جديد
                await db.collection('drivers').add({ name, phone, password });
                alert('تم إضافة السائق بنجاح');
                
                // إعادة تحميل البيانات
                loadAdminData();
            } catch (error) {
                console.error('خطأ في إضافة السائق:', error);
                alert('حدث خطأ أثناء إضافة السائق');
            }
        });

        // وظائف العميل
        async function loadClientData() {
            // تحميل قائمة الأماكن
            const locations = await getLocations();
            const fromSelect = document.getElementById('client-from');
            const toSelect = document.getElementById('client-to');
            
            fromSelect.innerHTML = '<option value="">اختر مكان الانطلاق</option>';
            toSelect.innerHTML = '<option value="">اختر مكان الوصول</option>';
            
            locations.forEach(location => {
                fromSelect.innerHTML += `<option value="${location}">${location}</option>`;
                toSelect.innerHTML += `<option value="${location}">${location}</option>`;
            });
            
            // الاستماع للتغيير في أماكن الانطلاق والوصول
            fromSelect.addEventListener('change', calculatePrice);
            toSelect.addEventListener('change', calculatePrice);
            
            // التحقق من وجود رحلة نشطة
            checkActiveTrip();
        }

        async function calculatePrice() {
            const from = document.getElementById('client-from').value;
            const to = document.getElementById('client-to').value;
            
            if (!from || !to) {
                document.getElementById('trip-price').textContent = '0';
                return;
            }
            
            try {
                const priceSnapshot = await db.collection('prices')
                    .where('from', '==', from)
                    .where('to', '==', to)
                    .get();
                
                if (!priceSnapshot.empty) {
                    priceSnapshot.forEach(doc => {
                        const price = doc.data().amount;
                        document.getElementById('trip-price').textContent = price;
                    });
                } else {
                    document.getElementById('trip-price').textContent = '0';
                    alert('لا يوجد سعر محدد لهذه الرحلة');
                }
            } catch (error) {
                console.error('خطأ في حساب السعر:', error);
            }
        }

        // طلب رحلة جديدة
        document.getElementById('request-trip-form').addEventListener('submit', async (e) => {
            e.preventDefault();
            const from = document.getElementById('client-from').value;
            const to = document.getElementById('client-to').value;
            const paymentMethod = document.getElementById('payment-method').value;
            const price = parseInt(document.getElementById('trip-price').textContent);
            
            if (!from || !to) {
                alert('يرجى اختيار مكان الانطلاق والوصول');
                return;
            }
            
            if (price <= 0) {
                alert('يرجى اختيار رحلة ذات سعر صحيح');
                return;
            }
            
            try {
                // إنشاء رحلة جديدة
                const tripRef = await db.collection('trips').add({
                    clientId: currentUser.uid,
                    clientPhone: currentUser.phone,
                    from,
                    to,
                    price,
                    paymentMethod,
                    status: 'pending',
                    createdAt: firebase.firestore.FieldValue.serverTimestamp()
                });
                
                // عرض تفاصيل الرحلة
                document.getElementById('current-from').textContent = from;
                document.getElementById('current-to').textContent = to;
                document.getElementById('current-price').textContent = price;
                document.getElementById('trip-status').textContent = 'بانتظار القبول';
                document.getElementById('driver-name').textContent = 'لم يتم التعيين بعد';
                
                document.querySelector('.trip-form').style.display = 'none';
                document.getElementById('active-trip').style.display = 'block';
                
                // متابعة حالة الرحلة
                monitorTrip(tripRef.id);
            } catch (error) {
                console.error('خطأ في طلب الرحلة:', error);
                alert('حدث خطأ أثناء طلب الرحلة');
            }
        });

        async function monitorTrip(tripId) {
            if (unsubscribeTripListener) {
                unsubscribeTripListener();
            }
            
            unsubscribeTripListener = db.collection('trips').doc(tripId)
                .onSnapshot(async (doc) => {
                    if (doc.exists) {
                        const trip = doc.data();
                        currentTrip = { id: doc.id, ...trip };
                        
                        // تحديث واجهة المستخدم بناءً على حالة الرحلة
                        document.getElementById('trip-status').textContent = 
                            trip.status === 'accepted' ? 'مقبولة' : 
                            trip.status === 'rejected' ? 'مرفوضة' : 
                            trip.status === 'in-progress' ? 'قيد التنفيذ' : 
                            trip.status === 'completed' ? 'منتهية' : 'بانتظار القبول';
                        
                        if (trip.driverId) {
                            const driverDoc = await db.collection('drivers').doc(trip.driverId).get();
                            if (driverDoc.exists) {
                                document.getElementById('driver-name').textContent = driverDoc.data().name;
                            }
                        }
                        
                        if (trip.status === 'in-progress') {
                            document.getElementById('end-trip-btn').disabled = false;
                        }
                        
                        if (trip.status === 'completed') {
                            document.getElementById('end-trip-btn').disabled = true;
                            setTimeout(() => {
                                alert('تم إنهاء الرحلة بنجاح');
                                document.querySelector('.trip-form').style.display = 'block';
                                document.getElementById('active-trip').style.display = 'none';
                            }, 1000);
                        }
                    }
                });
        }

        async function checkActiveTrip() {
            try {
                const tripSnapshot = await db.collection('trips')
                    .where('clientId', '==', currentUser.uid)
                    .where('status', 'in', ['pending', 'accepted', 'in-progress'])
                    .get();
                
                if (!tripSnapshot.empty) {
                    tripSnapshot.forEach(doc => {
                        currentTrip = { id: doc.id, ...doc.data() };
                        
                        document.getElementById('current-from').textContent = currentTrip.from;
                        document.getElementById('current-to').textContent = currentTrip.to;
                        document.getElementById('current-price').textContent = currentTrip.price;
                        document.getElementById('trip-status').textContent = 
                            currentTrip.status === 'accepted' ? 'مقبولة' : 
                            currentTrip.status === 'in-progress' ? 'قيد التنفيذ' : 'بانتظار القبول';
                        
                        if (currentTrip.driverId) {
                            db.collection('drivers').doc(currentTrip.driverId).get()
                                .then(driverDoc => {
                                    if (driverDoc.exists) {
                                        document.getElementById('driver-name').textContent = driverDoc.data().name;
                                    }
                                });
                        }
                        
                        document.querySelector('.trip-form').style.display = 'none';
                        document.getElementById('active-trip').style.display = 'block';
                        
                        monitorTrip(currentTrip.id);
                    });
                }
            } catch (error) {
                console.error('خطأ في التحقق من الرحلات النشطة:', error);
            }
        }

        function endTrip() {
            if (!currentTrip) return;
            
            db.collection('trips').doc(currentTrip.id).update({
                status: 'completed',
                completedAt: firebase.firestore.FieldValue.serverTimestamp()
            });
        }

        function sendMessage() {
            // هذه الوظيفة تحتاج إلى اكتمالها لترسل الرسائل بين العميل والسائق
            alert('سيتم إرسال الرسالة في التطبيق الكامل');
        }

        // وظائف السائق
        async function loadDriverData() {
            // تحميل إحصائيات السائق
            const tripsSnapshot = await db.collection('trips')
                .where('driverId', '==', currentUser.uid)
                .where('status', '==', 'completed')
                .get();
            
            document.getElementById('trips-count').textContent = tripsSnapshot.size;
            
            let totalEarnings = 0;
            tripsSnapshot.forEach(doc => {
                totalEarnings += doc.data().price * 0.8; // حصة السائق 80%
            });
            
            document.getElementById('total-earnings').textContent = `${totalEarnings} جنيه`;
            
            // الاستماع لطلبات الرحلات
            monitorTripRequests();
            
            // التحقق من وجود رحلة نشطة
            checkDriverActiveTrip();
        }

        function monitorTripRequests() {
            db.collection('trips')
                .where('status', '==', 'pending')
                .onSnapshot(snapshot => {
                    const requestsContainer = document.getElementById('trip-requests');
                    requestsContainer.innerHTML = '';
                    
                    snapshot.forEach(doc => {
                        const trip = doc.data();
                        const requestElement = document.createElement('div');
                        requestElement.className = 'request-card';
                        requestElement.innerHTML = `
                            <h4>طلب رحلة جديدة</h4>
                            <p>من: ${trip.from} إلى: ${trip.to}</p>
                            <p>السعر: ${trip.price} جنيه</p>
                            <p>طريقة الدفع: ${trip.paymentMethod}</p>
                            <button class="btn btn-driver" onclick="acceptTrip('${doc.id}')">قبول الطلب</button>
                            <button class="btn" onclick="rejectTrip('${doc.id}')">رفض الطلب</button>
                        `;
                        requestsContainer.appendChild(requestElement);
                    });
                });
        }

        async function acceptTrip(tripId) {
            try {
                // تحديث حالة الرحلة
                await db.collection('trips').doc(tripId).update({
                    status: 'accepted',
                    driverId: currentUser.uid,
                    acceptedAt: firebase.firestore.FieldValue.serverTimestamp()
                });
                
                // إخفاء طلبات الرحلات وعرض الرحلة الحالية
                document.getElementById('driver-waiting').style.display = 'none';
                document.getElementById('active-driver-trip').style.display = 'block';
                
                // تحميل تفاصيل الرحلة
                const tripDoc = await db.collection('trips').doc(tripId).get();
                if (tripDoc.exists) {
                    const trip = tripDoc.data();
                    document.getElementById('driver-current-from').textContent = trip.from;
                    document.getElementById('driver-current-to').textContent = trip.to;
                    document.getElementById('driver-current-price').textContent = trip.price;
                    document.getElementById('payment-type').textContent = trip.paymentMethod;
                    
                    currentTrip = { id: tripId, ...trip };
                    
                    // متابعة حالة الرحلة
                    monitorDriverTrip(tripId);
                }
            } catch (error) {
                console.error('خطأ في قبول الرحلة:', error);
                alert('حدث خطأ أثناء قبول الرحلة');
            }
        }

        function rejectTrip(tripId) {
            db.collection('trips').doc(tripId).update({
                status: 'rejected'
            });
        }

        async function checkDriverActiveTrip() {
            try {
                const tripSnapshot = await db.collection('trips')
                    .where('driverId', '==', currentUser.uid)
                    .where('status', 'in', ['accepted', 'in-progress'])
                    .get();
                
                if (!tripSnapshot.empty) {
                    tripSnapshot.forEach(doc => {
                        const trip = doc.data();
                        currentTrip = { id: doc.id, ...trip };
                        
                        document.getElementById('driver-current-from').textContent = trip.from;
                        document.getElementById('driver-current-to').textContent = trip.to;
                        document.getElementById('driver-current-price').textContent = trip.price;
                        document.getElementById('payment-type').textContent = trip.paymentMethod;
                        
                        document.getElementById('driver-waiting').style.display = 'none';
                        document.getElementById('active-driver-trip').style.display = 'block';
                        
                        monitorDriverTrip(doc.id);
                    });
                }
            } catch (error) {
                console.error('خطأ في التحقق من الرحلات النشطة:', error);
            }
        }

        function monitorDriverTrip(tripId) {
            db.collection('trips').doc(tripId)
                .onSnapshot((doc) => {
                    if (doc.exists) {
                        const trip = doc.data();
                        
                        if (trip.status === 'in-progress') {
                            document.getElementById('driver-end-trip-btn').disabled = false;
                        }
                        
                        if (trip.status === 'completed') {
                            document.getElementById('driver-end-trip-btn').disabled = true;
                            setTimeout(() => {
                                alert('تم إنهاء الرحلة بنجاح');
                                document.getElementById('driver-waiting').style.display = 'block';
                                document.getElementById('active-driver-trip').style.display = 'none';
                                
                                // إعادة تحميل إحصائيات السائق
                                loadDriverData();
                            }, 1000);
                        }
                    }
                });
        }

        function driverEndTrip() {
            if (!currentTrip) return;
            
            db.collection('trips').doc(currentTrip.id).update({
                status: 'in-progress',
                startedAt: firebase.firestore.FieldValue.serverTimestamp()
            });
        }

        function sendDriverMessage() {
            // هذه الوظيفة تحتاج إلى اكتمالها لترسل الرسائل بين العميل والسائق
            alert('سيتم إرسال الرسالة في التطبيق الكامل');
        }

        // وظائف مساعدة
        async function getLocations() {
            // في التطبيق الحقيقي، سيتم جلب الأماكن من Firebase
            return [
                "أبوغيات", "الزعاقة", "كفر صقر", "الشعراء", "المنشية الجديدة",
                "الموانسة", "كفر حماد"
            ];
        }

        function adminLogout() {
            currentUser = null;
            showSection('main-screen');
        }

        function clientLogout() {
            if (unsubscribeTripListener) {
                unsubscribeTripListener();
            }
            currentUser = null;
            currentTrip = null;
            showSection('main-screen');
        }

        function driverLogout() {
            currentUser = null;
            currentTrip = null;
            showSection('main-screen');
        }

        // تهيئة التطبيق
        document.addEventListener('DOMContentLoaded', function() {
            // إضافة بعض البيانات الأولية للعرض التوضيحي
            const fromSelect = document.getElementById('from-location');
            const toSelect = document.getElementById('to-location');
            
            getLocations().then(locations => {
                locations.forEach(location => {
                    fromSelect.innerHTML += `<option value="${location}">${location}</option>`;
                    toSelect.innerHTML += `<option value="${location}">${location}</option>`;
                });
            });
        });
    </script>
</body>
</html>
