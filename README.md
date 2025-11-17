<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>LeafSays - Real-time Chat</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-auth.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-firestore.js"></script>
    <style>
        :root {
            --primary-green: #128C7E;
            --light-green: #25D366;
            --dark-green: #075E54;
            --primary-blue: #34B7F1;
            --light-blue: #6BCFFF;
            --chat-bg: #E5DDD5;
            --message-sent: #DCF8C6;
            --message-received: #FFFFFF;
            --sidebar-bg: #FFFFFF;
            --text-dark: #2A2A2A;
            --text-light: #757575;
            --border-light: #E0E0E0;
        }
        
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        body {
            background-color: #f0f0f0;
            height: 100vh;
            display: flex;
            flex-direction: column;
        }
        
        /* Login Page */
        #login-page {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background: linear-gradient(135deg, var(--primary-green), var(--primary-blue));
            color: white;
            text-align: center;
            padding: 20px;
        }
        
        .login-container {
            background-color: white;
            border-radius: 20px;
            padding: 40px;
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.2);
            color: var(--text-dark);
            max-width: 400px;
            width: 100%;
        }
        
        .logo {
            display: flex;
            align-items: center;
            justify-content: center;
            margin-bottom: 20px;
        }
        
        .logo-icon {
            font-size: 36px;
            color: var(--primary-green);
            margin-right: 10px;
        }
        
        .logo-text {
            font-size: 28px;
            font-weight: bold;
            color: var(--primary-green);
        }
        
        .login-container h1 {
            color: var(--primary-green);
            margin-bottom: 10px;
        }
        
        .login-container p {
            color: var(--text-light);
            margin-bottom: 30px;
        }
        
        #login-btn {
            background-color: var(--primary-green);
            color: white;
            border: none;
            padding: 12px 24px;
            border-radius: 50px;
            font-size: 16px;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            margin: 0 auto;
            transition: background-color 0.3s;
        }
        
        #login-btn:hover {
            background-color: var(--dark-green);
        }
        
        /* Main App */
        #app {
            display: none;
            height: 100vh;
            flex-direction: column;
        }
        
        /* Header */
        .app-header {
            background-color: var(--primary-green);
            color: white;
            padding: 10px 15px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
        }
        
        .app-logo {
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .app-logo-icon {
            font-size: 24px;
        }
        
        .app-logo-text {
            font-size: 20px;
            font-weight: bold;
        }
        
        .user-info {
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .user-avatar {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            background-color: var(--light-blue);
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            color: white;
        }
        
        .user-details h3 {
            font-size: 16px;
        }
        
        .user-details p {
            font-size: 12px;
            opacity: 0.8;
        }
        
        .header-actions {
            display: flex;
            gap: 15px;
        }
        
        .header-actions i {
            cursor: pointer;
            font-size: 18px;
        }
        
        /* Main Content */
        .app-content {
            display: flex;
            flex: 1;
            overflow: hidden;
        }
        
        /* Sidebar */
        .sidebar {
            width: 30%;
            background-color: var(--sidebar-bg);
            border-right: 1px solid var(--border-light);
            display: flex;
            flex-direction: column;
        }
        
        .sidebar-header {
            padding: 15px;
            background-color: var(--sidebar-bg);
            border-bottom: 1px solid var(--border-light);
        }
        
        .search-container {
            display: flex;
            align-items: center;
            background-color: #f6f6f6;
            border-radius: 20px;
            padding: 8px 15px;
        }
        
        .search-container i {
            color: var(--text-light);
            margin-right: 10px;
        }
        
        .search-container input {
            border: none;
            background: transparent;
            width: 100%;
            outline: none;
        }
        
        .sidebar-tabs {
            display: flex;
            border-bottom: 1px solid var(--border-light);
        }
        
        .tab {
            flex: 1;
            text-align: center;
            padding: 15px;
            cursor: pointer;
            font-weight: 500;
            color: var(--text-light);
        }
        
        .tab.active {
            color: var(--primary-green);
            border-bottom: 2px solid var(--primary-green);
        }
        
        .contacts-list {
            flex: 1;
            overflow-y: auto;
        }
        
        .contact-item {
            display: flex;
            padding: 15px;
            border-bottom: 1px solid var(--border-light);
            cursor: pointer;
            transition: background-color 0.2s;
        }
        
        .contact-item:hover {
            background-color: #f5f5f5;
        }
        
        .contact-item.active {
            background-color: #e9f5eb;
        }
        
        .contact-avatar {
            width: 50px;
            height: 50px;
            border-radius: 50%;
            background-color: var(--light-blue);
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            color: white;
            margin-right: 15px;
        }
        
        .contact-info {
            flex: 1;
        }
        
        .contact-name {
            font-weight: 500;
            margin-bottom: 5px;
        }
        
        .contact-status {
            font-size: 13px;
            color: var(--text-light);
        }
        
        /* Chat Area */
        .chat-area {
            flex: 1;
            display: flex;
            flex-direction: column;
            background-color: var(--chat-bg);
            background-image: url("data:image/svg+xml,%3Csvg width='100' height='100' viewBox='0 0 100 100' xmlns='http://www.w3.org/2000/svg'%3E%3Cpath d='M11 18c3.866 0 7-3.134 7-7s-3.134-7-7-7-7 3.134-7 7 3.134 7 7 7zm48 25c3.866 0 7-3.134 7-7s-3.134-7-7-7-7 3.134-7 7 3.134 7 7 7zm-43-7c1.657 0 3-1.343 3-3s-1.343-3-3-3-3 1.343-3 3 1.343 3 3 3zm63 31c1.657 0 3-1.343 3-3s-1.343-3-3-3-3 1.343-3 3 1.343 3 3 3zM34 90c1.657 0 3-1.343 3-3s-1.343-3-3-3-3 1.343-3 3 1.343 3 3 3zm56-76c1.657 0 3-1.343 3-3s-1.343-3-3-3-3 1.343-3 3 1.343 3 3 3zM12 86c2.21 0 4-1.79 4-4s-1.79-4-4-4-4 1.79-4 4 1.79 4 4 4zm28-65c2.21 0 4-1.79 4-4s-1.79-4-4-4-4 1.79-4 4 1.79 4 4 4zm23-11c2.76 0 5-2.24 5-5s-2.24-5-5-5-5 2.24-5 5 2.24 5 5 5zm-6 60c2.21 0 4-1.79 4-4s-1.79-4-4-4-4 1.79-4 4 1.79 4 4 4zm29 22c2.76 0 5-2.24 5-5s-2.24-5-5-5-5 2.24-5 5 2.24 5 5 5zM32 63c2.76 0 5-2.24 5-5s-2.24-5-5-5-5 2.24-5 5 2.24 5 5 5zm57-13c2.76 0 5-2.24 5-5s-2.24-5-5-5-5 2.24-5 5 2.24 5 5 5zm-9-21c1.105 0 2-.895 2-2s-.895-2-2-2-2 .895-2 2 .895 2 2 2zM60 91c1.105 0 2-.895 2-2s-.895-2-2-2-2 .895-2 2 .895 2 2 2zM35 41c1.105 0 2-.895 2-2s-.895-2-2-2-2 .895-2 2 .895 2 2 2zM12 60c1.105 0 2-.895 2-2s-.895-2-2-2-2 .895-2 2 .895 2 2 2z' fill='%2391cfa0' fill-opacity='0.1' fill-rule='evenodd'/%3E%3C/svg%3E");
        }
        
        .chat-header {
            background-color: var(--sidebar-bg);
            padding: 15px;
            display: flex;
            align-items: center;
            border-bottom: 1px solid var(--border-light);
        }
        
        .chat-header-avatar {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            background-color: var(--light-blue);
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            color: white;
            margin-right: 15px;
        }
        
        .chat-header-info {
            flex: 1;
        }
        
        .chat-header-name {
            font-weight: 500;
        }
        
        .chat-header-status {
            font-size: 13px;
            color: var(--text-light);
        }
        
        .chat-messages {
            flex: 1;
            padding: 20px;
            overflow-y: auto;
            display: flex;
            flex-direction: column;
        }
        
        .message {
            max-width: 70%;
            padding: 10px 15px;
            margin-bottom: 15px;
            border-radius: 7.5px;
            position: relative;
            word-wrap: break-word;
        }
        
        .message-sent {
            align-self: flex-end;
            background-color: var(--message-sent);
            border-top-right-radius: 0;
        }
        
        .message-received {
            align-self: flex-start;
            background-color: var(--message-received);
            border-top-left-radius: 0;
        }
        
        .message-time {
            font-size: 11px;
            color: var(--text-light);
            text-align: right;
            margin-top: 5px;
        }
        
        .chat-input-container {
            display: flex;
            padding: 15px;
            background-color: var(--sidebar-bg);
            align-items: center;
        }
        
        .chat-input {
            flex: 1;
            padding: 12px 15px;
            border: none;
            border-radius: 20px;
            outline: none;
            margin: 0 10px;
        }
        
        .chat-action-btn {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            background-color: transparent;
            border: none;
            cursor: pointer;
            color: var(--text-light);
            font-size: 18px;
        }
        
        .chat-action-btn:hover {
            background-color: #f0f0f0;
        }
        
        .send-btn {
            background-color: var(--primary-green);
            color: white;
        }
        
        .send-btn:hover {
            background-color: var(--dark-green);
        }
        
        /* Add Friend Modal */
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.5);
            z-index: 100;
            justify-content: center;
            align-items: center;
        }
        
        .modal-content {
            background-color: white;
            border-radius: 10px;
            width: 90%;
            max-width: 400px;
            padding: 20px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
        }
        
        .modal-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 15px;
        }
        
        .modal-title {
            font-weight: 500;
            color: var(--text-dark);
        }
        
        .close-modal {
            background: none;
            border: none;
            font-size: 20px;
            cursor: pointer;
            color: var(--text-light);
        }
        
        .modal-body {
            margin-bottom: 20px;
        }
        
        .modal-input {
            width: 100%;
            padding: 12px;
            border: 1px solid var(--border-light);
            border-radius: 5px;
            margin-bottom: 15px;
        }
        
        .modal-btn {
            background-color: var(--primary-green);
            color: white;
            border: none;
            padding: 10px 15px;
            border-radius: 5px;
            cursor: pointer;
            width: 100%;
        }
        
        .modal-btn:hover {
            background-color: var(--dark-green);
        }
        
        /* User ID Display */
        .user-id-display {
            background-color: #f0f8ff;
            border: 1px dashed var(--primary-blue);
            border-radius: 5px;
            padding: 10px;
            margin-top: 15px;
            text-align: center;
            font-size: 14px;
        }
        
        .user-id {
            font-weight: bold;
            color: var(--primary-blue);
            margin-top: 5px;
        }
        
        /* Responsive */
        @media (max-width: 768px) {
            .sidebar {
                width: 100%;
                display: none;
            }
            
            .chat-area {
                display: none;
            }
            
            .sidebar.active {
                display: flex;
            }
            
            .chat-area.active {
                display: flex;
            }
        }
    </style>
</head>
<body>
    <!-- Login Page -->
    <div id="login-page">
        <div class="login-container">
            <div class="logo">
                <i class="fas fa-leaf logo-icon"></i>
                <div class="logo-text">LeafSays</div>
            </div>
            <h1>Selamat Datang di LeafSays</h1>
            <p>Login dengan akun Google untuk mulai chat real-time</p>
            <button id="login-btn">
                <i class="fab fa-google"></i> Login dengan Google
            </button>
        </div>
    </div>
    
    <!-- Main App -->
    <div id="app">
        <!-- Header -->
        <div class="app-header">
            <div class="app-logo">
                <i class="fas fa-leaf app-logo-icon"></i>
                <div class="app-logo-text">LeafSays</div>
            </div>
            <div class="user-info">
                <div class="user-avatar" id="user-avatar">U</div>
                <div class="user-details">
                    <h3 id="user-name">User</h3>
                    <p id="user-status">Online</p>
                </div>
            </div>
            <div class="header-actions">
                <i class="fas fa-user-plus" id="add-friend-btn"></i>
                <i class="fas fa-sign-out-alt" id="logout-btn"></i>
            </div>
        </div>
        
        <!-- Main Content -->
        <div class="app-content">
            <!-- Sidebar -->
            <div class="sidebar">
                <div class="sidebar-header">
                    <div class="search-container">
                        <i class="fas fa-search"></i>
                        <input type="text" placeholder="Cari atau mulai chat baru">
                    </div>
                </div>
                
                <div class="sidebar-tabs">
                    <div class="tab active">Chats</div>
                    <div class="tab">Kontak</div>
                </div>
                
                <div class="contacts-list" id="contacts-list">
                    <!-- Contacts will be loaded here -->
                </div>
            </div>
            
            <!-- Chat Area -->
            <div class="chat-area">
                <div class="chat-header">
                    <div class="chat-header-avatar" id="chat-header-avatar">L</div>
                    <div class="chat-header-info">
                        <div class="chat-header-name" id="chat-header-name">LeafSays Chat</div>
                        <div class="chat-header-status" id="chat-header-status">Pilih kontak untuk memulai percakapan</div>
                    </div>
                </div>
                
                <div class="chat-messages" id="chat-messages">
                    <div class="welcome-message">
                        <div class="message message-received">
                            <div class="message-text">Selamat datang di LeafSays! Tambahkan teman dengan User ID mereka untuk memulai percakapan.</div>
                            <div class="message-time">Sekarang</div>
                        </div>
                    </div>
                </div>
                
                <div class="chat-input-container">
                    <button class="chat-action-btn">
                        <i class="fas fa-paperclip"></i>
                    </button>
                    <input type="text" class="chat-input" placeholder="Ketik pesan..." id="message-input">
                    <button class="chat-action-btn send-btn" id="send-btn">
                        <i class="fas fa-paper-plane"></i>
                    </button>
                </div>
            </div>
        </div>
    </div>
    
    <!-- Add Friend Modal -->
    <div class="modal" id="add-friend-modal">
        <div class="modal-content">
            <div class="modal-header">
                <h3 class="modal-title">Tambah Teman</h3>
                <button class="close-modal">&times;</button>
            </div>
            <div class="modal-body">
                <p>Masukkan User ID teman Anda untuk menambahkannya ke daftar kontak:</p>
                <input type="text" class="modal-input" id="friend-id-input" placeholder="Masukkan User ID">
                <button class="modal-btn" id="add-friend-confirm">Tambah Teman</button>
                
                <div class="user-id-display">
                    <p>User ID Anda:</p>
                    <div class="user-id" id="user-id-display">Loading...</div>
                    <p style="font-size: 12px; margin-top: 5px;">Bagikan ID ini kepada teman Anda</p>
                </div>
            </div>
        </div>
    </div>

    <script>
        // Firebase configuration - GANTI DENGAN KONFIGURASI FIREBASE
const firebaseConfig = {
    apiKey: "AIzaSyBjPJj73wSIg_0ffEpTS4Z7ecBWJs6xRvI",
    authDomain: "leafsays-a8c84.firebaseapp.com",
    projectId: "leafsays-a8c84",
    storageBucket: "leafsays-a8c84.firebasestorage.app",
    messagingSenderId: "863298565588",
    appId: "1:863298565588:web:f379e717f118c9ed41d241"
};

        // Initialize Firebase
        firebase.initializeApp(firebaseConfig);
        const auth = firebase.auth();
        const db = firebase.firestore();

        // DOM Elements
        const loginPage = document.getElementById('login-page');
        const app = document.getElementById('app');
        const loginBtn = document.getElementById('login-btn');
        const logoutBtn = document.getElementById('logout-btn');
        const addFriendBtn = document.getElementById('add-friend-btn');
        const addFriendModal = document.getElementById('add-friend-modal');
        const closeModal = document.querySelector('.close-modal');
        const addFriendConfirm = document.getElementById('add-friend-confirm');
        const friendIdInput = document.getElementById('friend-id-input');
        const userIdDisplay = document.getElementById('user-id-display');
        const userAvatar = document.getElementById('user-avatar');
        const userName = document.getElementById('user-name');
        const contactsList = document.getElementById('contacts-list');
        const chatMessages = document.getElementById('chat-messages');
        const messageInput = document.getElementById('message-input');
        const sendBtn = document.getElementById('send-btn');
        const chatHeaderName = document.getElementById('chat-header-name');
        const chatHeaderAvatar = document.getElementById('chat-header-avatar');
        const chatHeaderStatus = document.getElementById('chat-header-status');

        // Global variables
        let currentUser = null;
        let selectedContact = null;
        let contacts = [];
        let userData = null;

        // Google Login
        loginBtn.addEventListener('click', () => {
            const provider = new firebase.auth.GoogleAuthProvider();
            auth.signInWithPopup(provider)
                .then((result) => {
                    // User signed in
                    currentUser = result.user;
                    setupUserProfile();
                    loginPage.style.display = 'none';
                    app.style.display = 'flex';
                    
                    // Generate and save user ID if not exists
                    generateUserId();
                    
                    // Load contacts
                    loadContacts();
                })
                .catch((error) => {
                    console.error('Login error:', error);
                    alert('Login gagal. Silakan coba lagi.');
                });
        });

        // Logout
        logoutBtn.addEventListener('click', () => {
            auth.signOut().then(() => {
                app.style.display = 'none';
                loginPage.style.display = 'flex';
                currentUser = null;
                selectedContact = null;
                contacts = [];
                contactsList.innerHTML = '';
                chatMessages.innerHTML = '';
            });
        });

        // Open add friend modal
        addFriendBtn.addEventListener('click', () => {
            addFriendModal.style.display = 'flex';
        });

        // Close modal
        closeModal.addEventListener('click', () => {
            addFriendModal.style.display = 'none';
        });

        // Add friend
        addFriendConfirm.addEventListener('click', () => {
            const friendId = friendIdInput.value.trim();
            if (friendId) {
                addFriend(friendId);
                friendIdInput.value = '';
                addFriendModal.style.display = 'none';
            } else {
                alert('Masukkan User ID yang valid');
            }
        });

        // Send message
        sendBtn.addEventListener('click', sendMessage);
        messageInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter') {
                sendMessage();
            }
        });

        // Setup user profile
        function setupUserProfile() {
            userAvatar.textContent = currentUser.displayName ? currentUser.displayName.charAt(0).toUpperCase() : 'U';
            userName.textContent = currentUser.displayName || 'User';
        }

        // Generate user ID
        function generateUserId() {
            // Check if user already has an ID
            db.collection('users').doc(currentUser.uid).get()
                .then((doc) => {
                    if (doc.exists) {
                        // User already has an ID
                        userData = doc.data();
                        userIdDisplay.textContent = userData.userId;
                    } else {
                        // Generate new user ID
                        const userId = 'USER_' + Math.random().toString(36).substr(2, 9).toUpperCase();
                        userIdDisplay.textContent = userId;
                        
                        // Save to Firestore
                        userData = {
                            uid: currentUser.uid,
                            email: currentUser.email,
                            displayName: currentUser.displayName,
                            photoURL: currentUser.photoURL,
                            userId: userId,
                            lastSeen: new Date(),
                            createdAt: new Date()
                        };
                        
                        db.collection('users').doc(currentUser.uid).set(userData);
                    }
                })
                .catch((error) => {
                    console.error('Error getting user data:', error);
                });
        }

        // Load contacts
        function loadContacts() {
            // Load user's contacts from Firestore
            db.collection('users').doc(currentUser.uid).collection('contacts')
                .onSnapshot((snapshot) => {
                    contacts = [];
                    snapshot.forEach((doc) => {
                        const contact = doc.data();
                        contacts.push(contact);
                    });
                    renderContacts();
                }, (error) => {
                    console.error('Error loading contacts:', error);
                });
        }

        // Render contacts
        function renderContacts() {
            contactsList.innerHTML = '';
            
            if (contacts.length === 0) {
                contactsList.innerHTML = '<div style="text-align: center; padding: 20px; color: var(--text-light);">Belum ada kontak. Tambahkan teman dengan User ID mereka.</div>';
                return;
            }
            
            contacts.forEach(contact => {
                const contactItem = document.createElement('div');
                contactItem.className = 'contact-item';
                contactItem.dataset.id = contact.userId;
                
                if (selectedContact && selectedContact.userId === contact.userId) {
                    contactItem.classList.add('active');
                }
                
                contactItem.innerHTML = `
                    <div class="contact-avatar">${contact.displayName ? contact.displayName.charAt(0).toUpperCase() : 'F'}</div>
                    <div class="contact-info">
                        <div class="contact-name">${contact.displayName || 'Friend'}</div>
                        <div class="contact-status">${contact.status || 'Online'}</div>
                    </div>
                `;
                
                contactItem.addEventListener('click', () => {
                    selectContact(contact);
                });
                
                contactsList.appendChild(contactItem);
            });
        }

        // Select contact
        function selectContact(contact) {
            selectedContact = contact;
            chatHeaderName.textContent = contact.displayName || 'Friend';
            chatHeaderAvatar.textContent = contact.displayName ? contact.displayName.charAt(0).toUpperCase() : 'F';
            chatHeaderStatus.textContent = 'Online';
            
            // Update active state in contacts list
            document.querySelectorAll('.contact-item').forEach(item => {
                item.classList.remove('active');
                if (item.dataset.id === contact.userId) {
                    item.classList.add('active');
                }
            });
            
            // Load messages for this contact
            loadMessages(contact.userId);
        }

        // Load messages
        function loadMessages(contactId) {
            // Clear chat messages
            chatMessages.innerHTML = '';
            
            // Load messages from Firestore
            const chatId = [currentUser.uid, contactId].sort().join('_');
            
            db.collection('chats').doc(chatId).collection('messages')
                .orderBy('timestamp', 'asc')
                .onSnapshot((snapshot) => {
                    chatMessages.innerHTML = '';
                    snapshot.forEach((doc) => {
                        const message = doc.data();
                        addMessageToChat(message.text, message.timestamp.toDate(), message.senderId === currentUser.uid ? 'me' : 'other');
                    });
                    
                    // Scroll to bottom
                    chatMessages.scrollTop = chatMessages.scrollHeight;
                }, (error) => {
                    console.error('Error loading messages:', error);
                });
        }

        // Add message to chat
        function addMessageToChat(text, timestamp, sender) {
            const messageDiv = document.createElement('div');
            messageDiv.className = `message ${sender === 'me' ? 'message-sent' : 'message-received'}`;
            
            const timeString = `${timestamp.getHours()}:${timestamp.getMinutes().toString().padStart(2, '0')}`;
            
            messageDiv.innerHTML = `
                <div class="message-text">${text}</div>
                <div class="message-time">${timeString}</div>
            `;
            
            chatMessages.appendChild(messageDiv);
        }

        // Send message
        function sendMessage() {
            const messageText = messageInput.value.trim();
            if (!messageText || !selectedContact) {
                alert('Pilih kontak terlebih dahulu atau ketik pesan');
                return;
            }
            
            const timestamp = new Date();
            
            // Generate chat ID (combination of both user IDs, sorted)
            const chatId = [currentUser.uid, selectedContact.uid].sort().join('_');
            
            // Save message to Firestore
            db.collection('chats').doc(chatId).collection('messages').add({
                text: messageText,
                senderId: currentUser.uid,
                senderName: currentUser.displayName,
                timestamp: timestamp,
                read: false
            })
            .then(() => {
                // Clear input
                messageInput.value = '';
            })
            .catch((error) => {
                console.error('Error sending message:', error);
                alert('Gagal mengirim pesan. Silakan coba lagi.');
            });
        }

        // Add friend
        function addFriend(friendId) {
            // Find user by userId
            db.collection('users').where('userId', '==', friendId).get()
                .then((snapshot) => {
                    if (snapshot.empty) {
                        alert('User dengan ID tersebut tidak ditemukan');
                        return;
                    }
                    
                    snapshot.forEach((doc) => {
                        const friendData = doc.data();
                        
                        // Check if already added
                        if (contacts.some(contact => contact.userId === friendId)) {
                            alert('Teman sudah ada di daftar kontak');
                            return;
                        }
                        
                        // Add to contacts
                        db.collection('users').doc(currentUser.uid).collection('contacts').doc(friendData.uid).set({
                            uid: friendData.uid,
                            userId: friendData.userId,
                            displayName: friendData.displayName,
                            photoURL: friendData.photoURL,
                            status: 'Online',
                            addedAt: new Date()
                        })
                        .then(() => {
                            alert(`Teman ${friendData.displayName} berhasil ditambahkan!`);
                        })
                        .catch((error) => {
                            console.error('Error adding friend:', error);
                            alert('Gagal menambahkan teman. Silakan coba lagi.');
                        });
                    });
                })
                .catch((error) => {
                    console.error('Error finding user:', error);
                    alert('Terjadi kesalahan. Silakan coba lagi.');
                });
        }

        // Auth state observer
        auth.onAuthStateChanged((user) => {
            if (user) {
                currentUser = user;
                setupUserProfile();
                loginPage.style.display = 'none';
                app.style.display = 'flex';
                generateUserId();
                loadContacts();
            } else {
                app.style.display = 'none';
                loginPage.style.display = 'flex';
            }
        });
    </script>
</body>
</html>

