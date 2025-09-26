<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نظام تتبع الطلاب</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f3f4f6;
            direction: rtl;
        }
        .container {
            max-width: 1200px;
        }
        .form-input, .form-select {
            @apply w-full rounded-md border border-gray-300 shadow-sm focus:border-indigo-300 focus:ring focus:ring-indigo-200 focus:ring-opacity-50 px-3 py-2;
        }
        .btn-primary {
            @apply bg-blue-600 text-white font-bold py-2 px-4 rounded-lg shadow-md hover:bg-blue-700 transition-colors duration-200;
        }
        .btn-secondary {
            @apply bg-gray-200 text-gray-700 font-bold py-2 px-4 rounded-lg shadow-md hover:bg-gray-300 transition-colors duration-200;
        }
        .modal {
            background-color: rgba(0, 0, 0, 0.5);
        }
        .loading-overlay {
            background-color: rgba(255, 255, 255, 0.7);
        }
        .table-row:nth-child(even) {
            background-color: #f9fafb;
        }
        .table-row:hover {
            background-color: #f3f4f6;
        }
    </style>
</head>
<body>
    <div class="container mx-auto p-4 md:p-8 space-y-8">
        <!-- Header -->
        <header class="text-center">
            <h1 class="text-4xl md:text-5xl font-extrabold text-blue-800 tracking-tight">
                نظام تتبع بيانات الطلاب
            </h1>
            <p class="mt-2 text-lg text-gray-600">
                <span id="userIdDisplay" class="font-mono text-xs md:text-sm bg-gray-200 rounded-full px-3 py-1 mt-2 inline-block"></span>
            </p>
        </header>

        <!-- Main Form -->
        <div class="bg-white rounded-xl shadow-lg p-6 md:p-8">
            <h2 class="text-2xl font-bold text-gray-800 mb-6">إدخال البيانات الثابتة</h2>
            <form id="fixed-data-form" class="space-y-6">
                <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                    <div>
                        <label for="college" class="block text-sm font-medium text-gray-700 mb-1">الكلية</label>
                        <select id="college" class="form-select">
                            <option value="">-- اختر كلية --</option>
                            <option value="هندسة البرمجيات">هندسة البرمجيات</option>
                            <option value="العلوم">العلوم</option>
                            <option value="الطب">الطب</option>
                            <option value="الصيدلة">الصيدلة</option>
                            <option value="التجارة">التجارة</option>
                            <option value="الاداب">الآداب</option>
                        </select>
                    </div>
                    <div>
                        <label for="level" class="block text-sm font-medium text-gray-700 mb-1">المستوى الدراسي</label>
                        <select id="level" class="form-select">
                            <option value="">-- اختر مستوى --</option>
                            <option value="1">1</option>
                            <option value="2">2</option>
                            <option value="3">3</option>
                            <option value="4">4</option>
                            <option value="5">5</option>
                        </select>
                    </div>
                    <div>
                        <label for="subject" class="block text-sm font-medium text-gray-700 mb-1">المادة</label>
                        <input type="text" id="subject" class="form-input" placeholder="أدخل اسم المادة">
                    </div>
                    <div>
                        <label for="exam-date" class="block text-sm font-medium text-gray-700 mb-1">تاريخ الاختبار</label>
                        <input type="date" id="exam-date" class="form-input">
                    </div>
                </div>
                <div class="flex justify-center mt-6">
                    <button type="button" id="add-students-btn" class="btn-primary w-full md:w-auto">إضافة الطلاب</button>
                </div>
            </form>
        </div>

        <!-- Dynamic Form Section -->
        <div id="dynamic-fields-container" class="hidden bg-white rounded-xl shadow-lg p-6 md:p-8">
            <h2 class="text-2xl font-bold text-gray-800 mb-6">إدخال بيانات الطلاب</h2>
            <form id="dynamic-form" onsubmit="addStudents(event)">
            </form>
        </div>

        <!-- Data Display Section -->
        <div id="table-container" class="bg-white rounded-xl shadow-lg p-6 md:p-8 hidden">
            <h2 class="text-2xl font-bold text-gray-800 mb-6">كشف بيانات الطلاب</h2>
            
            <!-- Filter & Search Controls -->
            <div class="flex flex-col md:flex-row md:items-center justify-between mb-4 space-y-4 md:space-y-0 md:space-x-4">
                <div class="w-full md:w-1/3">
                    <label for="search-input" class="block text-sm font-medium text-gray-700 sr-only">بحث</label>
                    <input type="text" id="search-input" oninput="filterTable()" placeholder="ابحث عن اسم أو مادة..." class="form-input">
                </div>
                <div class="w-full md:w-1/3">
                    <label for="filter-college" class="block text-sm font-medium text-gray-700 sr-only">تصفية حسب الكلية</label>
                    <select id="filter-college" onchange="filterTable()" class="form-select">
                        <option value="">جميع الكليات</option>
                    </select>
                </div>
                <div class="w-full md:w-1/3">
                    <label for="filter-level" class="block text-sm font-medium text-gray-700 sr-only">تصفية حسب المستوى</label>
                    <select id="filter-level" onchange="filterTable()" class="form-select">
                        <option value="">جميع المستويات</option>
                        <option value="1">1</option>
                        <option value="2">2</option>
                        <option value="3">3</option>
                        <option value="4">4</option>
                        <option value="5">5</option>
                    </select>
                </div>
            </div>

            <div class="overflow-x-auto">
                <table id="data-table" class="min-w-full divide-y divide-gray-200 rounded-xl overflow-hidden">
                    <thead class="bg-gray-100">
                        <tr>
                            <th scope="col" class="px-6 py-3 text-right text-xs font-bold text-gray-500 uppercase tracking-wider">اسم الطالب</th>
                            <th scope="col" class="px-6 py-3 text-right text-xs font-bold text-gray-500 uppercase tracking-wider">الكلية</th>
                            <th scope="col" class="px-6 py-3 text-right text-xs font-bold text-gray-500 uppercase tracking-wider">المستوى</th>
                            <th scope="col" class="px-6 py-3 text-right text-xs font-bold text-gray-500 uppercase tracking-wider">المادة</th>
                            <th scope="col" class="px-6 py-3 text-right text-xs font-bold text-gray-500 uppercase tracking-wider">تاريخ الاختبار</th>
                            <th scope="col" class="px-6 py-3 text-right text-xs font-bold text-gray-500 uppercase tracking-wider">المبلغ المتبقي</th>
                            <th scope="col" class="px-6 py-3 text-right text-xs font-bold text-gray-500 uppercase tracking-wider action-col">إجراءات</th>
                        </tr>
                    </thead>
                    <tbody id="student-table-body" class="bg-white divide-y divide-gray-200">
                        <!-- Student data will be rendered here -->
                    </tbody>
                </table>
            </div>

            <!-- Export Buttons -->
            <div id="export-buttons-container" class="flex justify-center space-x-4 mt-6">
                <button onclick="saveAs('xlsx')" class="btn-secondary">تصدير إلى Excel</button>
                <button onclick="saveAs('pdf')" class="btn-secondary">تصدير إلى PDF</button>
                <button onclick="saveAs('docx')" class="btn-secondary">تصدير إلى Word</button>
            </div>
        </div>

        <!-- Modals -->
        
        <!-- Add Students Count Modal -->
        <div id="countModal" class="modal fixed inset-0 z-50 flex items-center justify-center p-4 hidden">
            <div class="bg-white rounded-lg shadow-xl p-6 w-full max-w-sm">
                <h3 class="text-xl font-bold text-gray-900 mb-4 text-center">عدد الطلاب المراد إضافتهم</h3>
                <input type="number" id="numStudentsInput" class="form-input mb-4" placeholder="أدخل عدداً صحيحاً" min="1" required>
                <div class="flex justify-between space-x-2">
                    <button type="button" onclick="createStudentFields()" class="btn-primary flex-1">متابعة</button>
                    <button type="button" onclick="closeModal('countModal')" class="btn-secondary flex-1">إلغاء</button>
                </div>
            </div>
        </div>

        <!-- Edit Modal -->
        <div id="editModal" class="modal fixed inset-0 z-50 flex items-center justify-center p-4 hidden">
            <div class="bg-white rounded-lg shadow-xl p-6 w-full max-w-md">
                <h3 class="text-2xl font-bold text-gray-900 mb-4 text-center">تعديل بيانات الطالب</h3>
                <form id="edit-form" onsubmit="updateStudent(event)">
                    <input type="hidden" id="edit-doc-id">
                    <div class="space-y-4">
                        <div>
                            <label for="edit-student-name" class="block text-sm font-medium text-gray-700">اسم الطالب</label>
                            <input type="text" id="edit-student-name" class="form-input" required>
                        </div>
                        <div>
                            <label for="edit-amount-due" class="block text-sm font-medium text-gray-700">المبلغ المتبقي</label>
                            <input type="number" id="edit-amount-due" class="form-input" required>
                        </div>
                        <div>
                            <label for="edit-college" class="block text-sm font-medium text-gray-700">الكلية</label>
                            <select id="edit-college" class="form-select" required></select>
                        </div>
                        <div>
                            <label for="edit-level" class="block text-sm font-medium text-gray-700">المستوى</label>
                            <select id="edit-level" class="form-select" required>
                                <option value="1">1</option>
                                <option value="2">2</option>
                                <option value="3">3</option>
                                <option value="4">4</option>
                                <option value="5">5</option>
                            </select>
                        </div>
                        <div>
                            <label for="edit-subject" class="block text-sm font-medium text-gray-700">المادة</label>
                            <input type="text" id="edit-subject" class="form-input" required>
                        </div>
                        <div>
                            <label for="edit-exam-date" class="block text-sm font-medium text-gray-700">تاريخ الاختبار</label>
                            <input type="date" id="edit-exam-date" class="form-input" required>
                        </div>
                    </div>
                    <div class="flex justify-between space-x-2 mt-6">
                        <button type="submit" class="btn-primary flex-1">حفظ التغييرات</button>
                        <button type="button" onclick="closeModal('editModal')" class="btn-secondary flex-1">إلغاء</button>
                    </div>
                </form>
            </div>
        </div>
        
        <!-- Custom Confirm Modal for Delete -->
        <div id="deleteConfirmModal" class="modal fixed inset-0 z-50 flex items-center justify-center p-4 hidden">
            <div class="bg-white rounded-lg shadow-xl p-6 w-full max-w-sm text-center">
                <h3 class="text-xl font-bold text-gray-900 mb-4">هل أنت متأكد؟</h3>
                <p class="text-gray-600 mb-6">سيتم حذف هذا الطالب بشكل دائم.</p>
                <div class="flex justify-between space-x-2">
                    <button id="confirmDeleteBtn" class="btn-primary flex-1 bg-red-600 hover:bg-red-700">نعم، احذف</button>
                    <button onclick="closeModal('deleteConfirmModal')" class="btn-secondary flex-1">إلغاء</button>
                </div>
            </div>
        </div>

        <!-- General Message Modal -->
        <div id="messageModal" class="modal fixed inset-0 z-50 flex items-center justify-center p-4 hidden">
            <div class="bg-white rounded-lg shadow-xl p-6 w-full max-w-sm text-center">
                <h3 id="messageTitle" class="text-xl font-bold text-gray-900 mb-2"></h3>
                <p id="messageBody" class="text-gray-600 mb-4"></p>
                <button onclick="closeModal('messageModal')" class="btn-primary w-full">إغلاق</button>
            </div>
        </div>
        
        <!-- Loading Overlay -->
        <div id="loadingOverlay" class="loading-overlay fixed inset-0 z-50 flex items-center justify-center hidden">
            <div class="flex items-center space-x-2 text-gray-700">
                <div class="w-8 h-8 rounded-full animate-spin border-4 border-blue-600 border-t-transparent"></div>
                <span>جاري المعالجة...</span>
            </div>
        </div>

    </div>

    <!-- External Libraries -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/docxtemplater/3.42.3/docxtemplater.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/docx/7.8.2/index.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/FileSaver.js/2.0.5/FileSaver.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.28/jspdf.plugin.autotable.min.js"></script>
    <script src="https://cdn.sheetjs.com/xlsx-0.20.1/package/xlsx.mjs"></script>

    <!-- Main Application Script -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, onAuthStateChanged, signInWithCustomToken } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, collection, addDoc, onSnapshot, deleteDoc, doc, updateDoc, query } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";
        
        // Use provided global variables
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const firebaseConfig = JSON.parse(typeof __firebase_config !== 'undefined' ? __firebase_config : '{}');
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;

        // Initialize Firebase
        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);
        const db = getFirestore(app);

        let userId = null;
        let allStudentsData = [];
        let docIdToDelete = null;

        // DOM Elements
        const addStudentsBtn = document.getElementById('add-students-btn');
        const countModal = document.getElementById('countModal');
        const numStudentsInput = document.getElementById('numStudentsInput');
        const dynamicFieldsContainer = document.getElementById('dynamic-fields-container');
        const dynamicForm = document.getElementById('dynamic-form');
        const studentTableBody = document.getElementById('student-table-body');
        const messageModal = document.getElementById('messageModal');
        const loadingOverlay = document.getElementById('loadingOverlay');
        const searchInput = document.getElementById('search-input');
        const filterCollege = document.getElementById('filter-college');
        const collegeDropdown = document.getElementById('college');
        const editModal = document.getElementById('editModal');
        const editDocId = document.getElementById('edit-doc-id');
        const editStudentName = document.getElementById('edit-student-name');
        const editAmountDue = document.getElementById('edit-amount-due');
        const editCollege = document.getElementById('edit-college');
        const editLevel = document.getElementById('edit-level');
        const editSubject = document.getElementById('edit-subject');
        const editExamDate = document.getElementById('edit-exam-date');
        const deleteConfirmModal = document.getElementById('deleteConfirmModal');
        const confirmDeleteBtn = document.getElementById('confirmDeleteBtn');
        const exportButtonsContainer = document.getElementById('export-buttons-container');
        const actionColumnHeader = document.querySelector('.action-col');
        
        // Firestore collection reference
        const studentsCollection = () => collection(db, `artifacts/${appId}/users/${userId}/students`);

        // --- Authentication Logic ---
        onAuthStateChanged(auth, async (user) => {
            if (user) {
                userId = user.uid;
                document.getElementById('userIdDisplay').textContent = `ID المستخدم: ${userId}`;
                populateCollegeFilter();
                populateEditCollegeDropdown();
                listenForStudents();
            } else {
                try {
                    // Use the provided custom token if available, otherwise sign in anonymously
                    if (initialAuthToken) {
                        await signInWithCustomToken(auth, initialAuthToken);
                    } else {
                        await signInAnonymously(auth);
                    }
                } catch (error) {
                    console.error("Error during authentication:", error);
                    showMessage('خطأ في الاتصال', 'تعذر تسجيل الدخول. يرجى التحقق من إعدادات Firebase.');
                }
            }
        });

        // Event listener for the delete confirmation button
        confirmDeleteBtn.addEventListener('click', async () => {
            if (docIdToDelete) {
                await deleteRow(docIdToDelete);
                docIdToDelete = null; 
            }
            closeModal('deleteConfirmModal');
        });

        function populateCollegeFilter() {
            const options = Array.from(collegeDropdown.options);
            filterCollege.innerHTML = '<option value="">جميع الكليات</option>';
            options.forEach(option => {
                if (option.value !== "") {
                    const newOption = document.createElement('option');
                    newOption.value = option.value;
                    newOption.textContent = option.textContent;
                    filterCollege.appendChild(newOption);
                }
            });
        }

        function populateEditCollegeDropdown() {
             const options = Array.from(collegeDropdown.options);
            editCollege.innerHTML = ''; 
            options.forEach(option => {
                if (option.value !== "") {
                    const newOption = document.createElement('option');
                    newOption.value = option.value;
                    newOption.textContent = option.textContent;
                    editCollege.appendChild(newOption);
                }
            });
        }
        
        // --- Modal/Message Functions ---
        function showMessage(title, body) {
            document.getElementById('messageTitle').textContent = title;
            document.getElementById('messageBody').textContent = body;
            messageModal.style.display = 'flex';
        }

        window.closeModal = function(modalId) {
            const modal = document.getElementById(modalId);
            modal.style.display = 'none';
        }

        window.addEventListener('load', () => {
             if (addStudentsBtn) {
                addStudentsBtn.addEventListener('click', () => {
                    const college = document.getElementById('college').value;
                    const level = document.getElementById('level').value;
                    const subject = document.getElementById('subject').value;
                    const examDate = document.getElementById('exam-date').value;

                    if (!college || !level || !subject || !examDate) {
                        showMessage('بيانات ثابتة مفقودة', 'الرجاء ملء جميع الحقول الثابتة أولاً (الكلية، المستوى، المادة، التاريخ).');
                        return;
                    }
                    countModal.style.display = 'flex';
                    numStudentsInput.focus();
                });
            }
        });

        window.createStudentFields = function() {
            const numStudents = parseInt(numStudentsInput.value, 10);
            if (numStudents > 0) {
                dynamicForm.innerHTML = '';
                for (let i = 0; i < numStudents; i++) {
                    const studentFields = document.createElement('div');
                    studentFields.classList.add('grid', 'grid-cols-1', 'md:grid-cols-2', 'gap-4', 'mb-4', 'md:col-span-2', 'p-4', 'bg-gray-50', 'rounded-lg');
                    studentFields.innerHTML = `
                        <div>
                            <label for="student-name-${i}" class="block text-sm font-medium text-gray-700 mb-1">اسم الطالب ${i + 1}</label>
                            <input type="text" id="student-name-${i}" class="form-input" placeholder="أدخل اسم الطالب" required>
                        </div>
                        <div>
                            <label for="amount-due-${i}" class="block text-sm font-medium text-gray-700 mb-1">المبلغ المتبقي</label>
                            <input type="number" id="amount-due-${i}" class="form-input" placeholder="أدخل المبلغ المتبقي" required min="0" value="0">
                        </div>
                    `;
                    dynamicForm.appendChild(studentFields);
                }
                const submitBtn = document.createElement('div');
                submitBtn.classList.add('md:col-span-2', 'flex', 'justify-center', 'mt-4');
                submitBtn.innerHTML = '<button type="submit" class="w-full md:w-auto px-6 py-2 btn-primary">إضافة إلى الكشف</button>';
                dynamicForm.appendChild(submitBtn);
                dynamicFieldsContainer.classList.remove('hidden');
                closeModal('countModal');
                dynamicFieldsContainer.scrollIntoView({ behavior: 'smooth' });
            } else {
                showMessage('خطأ في الإدخال', 'الرجاء إدخال عدد صحيح موجب.');
            }
        }

        window.addStudents = async function(e) {
            e.preventDefault();

            const college = document.getElementById('college').value;
            const level = document.getElementById('level').value;
            const subject = document.getElementById('subject').value;
            const examDate = document.getElementById('exam-date').value;

            loadingOverlay.style.display = 'flex'; 
            const numStudents = dynamicForm.querySelectorAll('input[id^="student-name"]').length;
            const studentsToAdd = [];

            for (let i = 0; i < numStudents; i++) {
                const studentNameInput = document.getElementById(`student-name-${i}`);
                const amountDueInput = document.getElementById(`amount-due-${i}`);

                if (studentNameInput && amountDueInput) {
                    const studentName = studentNameInput.value.trim();
                    const amountDue = parseFloat(amountDueInput.value);

                    if (studentName && !isNaN(amountDue)) {
                        studentsToAdd.push({
                            studentName,
                            college,
                            level,
                            subject,
                            examDate,
                            amountDue: amountDue,
                            timestamp: new Date().toISOString()
                        });
                    }
                }
            }
            
            if (studentsToAdd.length === 0) {
                loadingOverlay.style.display = 'none';
                showMessage('لا توجد بيانات', 'الرجاء إدخال أسماء ومبالغ الطلاب المراد إضافتهم.');
                return;
            }

            try {
                for (const student of studentsToAdd) {
                    await addDoc(studentsCollection(), student);
                }
                showMessage('نجاح', `تم إضافة ${studentsToAdd.length} طالب إلى الكشف بنجاح.`);
                dynamicForm.innerHTML = ''; 
                dynamicFieldsContainer.classList.add('hidden');
            } catch (e) {
                showMessage('خطأ في الحفظ', `حدث خطأ أثناء إضافة البيانات: ${e.message}`);
            } finally {
                loadingOverlay.style.display = 'none'; 
            }
        }

        function renderTable(data) {
            studentTableBody.innerHTML = '';
            
            data.forEach((item) => {
                const newRow = document.createElement('tr');
                newRow.classList.add('table-row');
                newRow.innerHTML = `
                    <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">${item.studentName}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${item.college}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${item.level}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${item.subject}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${item.examDate}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${item.amountDue.toFixed(2)}</td> 
                    <td class="px-6 py-4 whitespace-nowrap text-right text-sm font-medium action-col-data">
                        <button onclick="openEditModal('${item.id}')" class="text-blue-600 hover:text-blue-900 action-button ml-2">تعديل</button>
                        <button onclick="showDeleteConfirm('${item.id}')" class="text-red-600 hover:text-red-900 action-button">حذف</button>
                    </td>
                `;
                studentTableBody.appendChild(newRow);
            });

            const tableContainer = document.getElementById('table-container');
            if (data.length > 0) {
                tableContainer.classList.remove('hidden');
            } else if (allStudentsData.length === 0) {
                 tableContainer.classList.add('hidden');
            }
        }

        window.filterTable = function() {
            const searchTerm = searchInput.value.toLowerCase().trim();
            const collegeFilter = filterCollege.value;
            const levelFilter = document.getElementById('filter-level').value;

            const filteredData = allStudentsData.filter(student => {
                const matchesSearch = searchTerm === '' ||
                                      student.studentName.toLowerCase().includes(searchTerm) ||
                                      student.subject.toLowerCase().includes(searchTerm);
                
                const matchesCollege = collegeFilter === '' || student.college === collegeFilter;
                const matchesLevel = levelFilter === '' || student.level === levelFilter;

                return matchesSearch && matchesCollege && matchesLevel;
            });

            renderTable(filteredData);
        }

        function listenForStudents() {
            const studentQuery = query(studentsCollection());

            onSnapshot(studentQuery, (querySnapshot) => {
                allStudentsData = [];
                querySnapshot.forEach((doc) => {
                    const student = doc.data();
                    student.id = doc.id; 
                    allStudentsData.push(student);
                });

                // Sort data by timestamp locally
                allStudentsData.sort((a, b) => new Date(b.timestamp) - new Date(a.timestamp));

                filterTable(); 

                if (allStudentsData.length === 0) {
                    document.getElementById('table-container').classList.add('hidden');
                }

            }, (error) => {
                console.error("Error listening to Firestore:", error);
                showMessage('خطأ في استرداد البيانات', 'حدث خطأ أثناء جلب بيانات الطلاب من قاعدة البيانات.');
            });
        }

        window.showDeleteConfirm = function(docId) {
            docIdToDelete = docId;
            deleteConfirmModal.style.display = 'flex';
        }

        async function deleteRow(docId) {
            loadingOverlay.style.display = 'flex';
            try {
                await deleteDoc(doc(studentsCollection(), docId));
                showMessage('تم الحذف', 'تم حذف الطالب بنجاح.');
            } catch (e) {
                showMessage('خطأ في الحذف', `حدث خطأ أثناء حذف الطالب: ${e.message}`);
            } finally {
                loadingOverlay.style.display = 'none';
            }
        }
        
        window.openEditModal = function(docId) {
            const student = allStudentsData.find(s => s.id === docId);
            if (student) {
                editDocId.value = docId; 
                editStudentName.value = student.studentName;
                editAmountDue.value = student.amountDue.toString();
                editCollege.value = student.college;
                editLevel.value = student.level;
                editSubject.value = student.subject;
                editExamDate.value = student.examDate;

                editModal.style.display = 'flex';
            } else {
                showMessage('خطأ', 'لم يتم العثور على بيانات الطالب.');
            }
        }

        window.updateStudent = async function(e) {
            e.preventDefault();
            loadingOverlay.style.display = 'flex';
            
            const id = editDocId.value;
            
            const updatedData = {
                studentName: editStudentName.value.trim(),
                amountDue: parseFloat(editAmountDue.value),
                college: editCollege.value,
                level: editLevel.value,
                subject: editSubject.value.trim(),
                examDate: editExamDate.value,
            };
            
            if (!id || !updatedData.studentName || isNaN(updatedData.amountDue) || !updatedData.college || !updatedData.level || !updatedData.subject || !updatedData.examDate) {
                loadingOverlay.style.display = 'none';
                showMessage('خطأ في البيانات', 'الرجاء التأكد من صحة جميع المدخلات.');
                return;
            }

            try {
                await updateDoc(doc(studentsCollection(), id), updatedData);
                closeModal('editModal');
                showMessage('نجاح', 'تم تحديث بيانات الطالب بنجاح.');
            } catch (e) {
                showMessage('خطأ في التحديث', `حدث خطأ أثناء تحديث البيانات: ${e.message}`);
            } finally {
                loadingOverlay.style.display = 'none';
            }
        }

        window.saveAs = async function(format) {
            const table = document.querySelector('#data-table');
            if (table.tBodies[0].rows.length === 0) {
                showMessage("لا توجد بيانات", "لا توجد بيانات في الجدول للتصدير.");
                return;
            }

            loadingOverlay.style.display = 'flex'; 
            
            const actionColumnHeader = document.querySelector('.action-col');
            
            exportButtonsContainer.style.display = 'none';
            if (actionColumnHeader) actionColumnHeader.style.display = 'none';
            document.querySelectorAll('.action-col-data').forEach(el => el.style.display = 'none');
            
            await new Promise(resolve => setTimeout(resolve, 50));

            try {
                if (format === 'xlsx') {
                    const ws = XLSX.utils.table_to_sheet(table);
                    const wb = XLSX.utils.book_new();
                    XLSX.utils.book_append_sheet(wb, ws, "Students");
                    XLSX.writeFile(wb, "كشف_بيانات_الطلاب.xlsx");
                } else if (format === 'pdf') {
                    const { jsPDF } = window.jspdf;
                    const doc = new jsPDF();
                    const headers = ["اسم الطالب", "الكلية", "المستوى", "المادة", "تاريخ الاختبار", "المبلغ المتبقي"];
                    const data = Array.from(document.querySelectorAll('#student-table-body tr')).map(row => {
                        return Array.from(row.querySelectorAll('td')).slice(0, 6).map(cell => cell.textContent);
                    });
                    
                    doc.text("كشف تفصيلي ببيانات الطلاب", 105, 20, null, null, "center");

                    doc.autoTable({
                        startY: 30,
                        head: [headers.reverse()], 
                        body: data.map(row => row.reverse()), 
                        theme: 'grid',
                        styles: {
                            halign: 'right', 
                            cellPadding: 2,
                            fontSize: 10,
                            direction: 'rtl' 
                        },
                        headStyles: {
                            fontStyle: 'bold',
                            halign: 'right',
                            fillColor: [226, 232, 240]
                        },
                    });
                    doc.save('كشف_بيانات_الطلاب.pdf');
                } else if (format === 'docx') {
                    const tableRows = Array.from(table.tBodies[0].rows);
                    const tableData = tableRows.map(row => 
                        Array.from(row.cells).slice(0, 6).map(cell => new docx.Paragraph({
                            children: [new docx.TextRun({ text: cell.textContent, rightToLeft: true, font: { name: "Arial" } })],
                        }))
                    );
                    const tableHeaders = Array.from(table.tHead.rows[0].cells).slice(0, 6).map(header => new docx.Paragraph({
                        children: [new docx.TextRun({ text: header.textContent, bold: true, rightToLeft: true, font: { name: "Arial" } })],
                    }));

                    const doc = new docx.Document({
                        sections: [{
                            properties: { page: { size: { orientation: docx.PageOrientation.PORTRAIT, width: 11906, height: 16838 } } },
                            children: [
                                new docx.Paragraph({
                                    children: [new docx.TextRun({ text: "كشف تفصيلي ببيانات الطلاب", bold: true, size: 36, rightToLeft: true, font: { name: "Arial" } })],
                                    alignment: docx.AlignmentType.CENTER,
                                }),
                                new docx.Table({
                                    rows: [
                                        new docx.TableRow({ children: tableHeaders.reverse() }),
                                        ...tableData.map(row => new docx.TableRow({ children: row.reverse() }))
                                    ],
                                    margins: { top: 200, bottom: 200, left: 200, right: 200 },
                                }),
                            ],
                        }],
                    });

                    await docx.Packer.toBlob(doc).then(blob => {
                        saveAs(blob, "كشف_بيانات_الطلاب.docx");
                    });
                }
                
                showMessage('تم التصدير', `تم حفظ الكشف بنجاح بصيغة ${format.toUpperCase()}.`);

            } catch (e) {
                console.error("Export Error:", e);
                showMessage('خطأ في التصدير', `حدث خطأ: ${e.message}. تأكد من تحديث المتصفح.`);
            } finally {
                exportButtonsContainer.style.display = 'flex';
                if (actionColumnHeader) actionColumnHeader.style.display = 'table-cell';
                document.querySelectorAll('.action-col-data').forEach(el => el.style.display = 'table-cell');
                loadingOverlay.style.display = 'none';
            }
        }
    </script>
</body>
</html>
