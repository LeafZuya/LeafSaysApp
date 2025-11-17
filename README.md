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
        /* CSS tetap sama seperti sebelumnya */
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
        
        /* Tambahkan loading indicator */
        .loading {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(255,255,255,0.8);
            z-index: 1000;
            justify-content: center;
            align-items: center;
            flex-direction: column;
        }
        
        .loading-spinner {
            width: 50px;
            height: 50px;
            border: 5px solid #f3f3f3;
            border-top: 5px solid var(--primary-green);
            border-radius: 50%;
            animation: spin 1s linear infinite;
        }
        
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        
        /* CSS lainnya tetap sama... */
    </style>
</head>
<body>
    <!-- Loading Indicator -->
    <div class="loading" id="loading">
        <div class="loading-spinner"></div>
        <p style="margin-top: 20px; color: var(--primary-green);">Loading LeafSays...</p>
    </div>

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
            <div style="margin-top: 20px; font-size: 12px; color: var(--text-light);">
                Hosted on GitHub Pages
            </div>
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
                    <div style="text-align: center; padding: 20px; color: var(--text-light);">
                        <i class="fas fa-users" style="font-size: 24px; margin-bottom: 10px;"></i>
                        <p>Belum ada kontak. Tambahkan teman dengan User ID mereka.</p>
                    </div>
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
                    <div class="message message-received">
                        <div class="message-text">Selamat datang di LeafSays! 🌿</div>
                        <div class="message-text" style="margin-top: 5px;">Tambahkan teman dengan User ID mereka untuk memulai percakapan real-time.</div>
                        <div class="message-time">Sekarang</div>
                    </div>
                </div>
                
                <div class="chat-input-container">
                    <button class="chat-action-btn">
                        <i class="fas fa-paperclip"></i>
                    </button>
                    <input type="text" class="chat-input" placeholder="Ketik pesan..." id="message-input" disabled>
                    <button class="chat-action-btn send-btn" id="send-btn" disabled>
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
        // Firebase configuration - PAKAI KONFIGURASI ANDA
        const firebaseConfig = {
            apiKey: "AIzaSyBjPJj73wSIg_0ffEpTS4Z7ecBWJs6xRvI",
            authDomain: "leafsays-a8c84.firebaseapp.com",
            projectId: "leafsays-a8c84",
            storageBucket: "leafsays-a8c84.firebasestorage.app",
            messagingSenderId: "863298565588",
            appId: "1:863298565588:web:f379e717f118c9ed41d241"
        };

        // Show loading initially
        document.getElementById('loading').style.display = 'flex';

        // Initialize Firebase
        try {
            firebase.initializeApp(firebaseConfig);
            console.log('Firebase initialized successfully');
        } catch (error) {
            console.error('Firebase initialization error:', error);
            alert('Error: Gagal menginisialisasi Firebase. Periksa koneksi internet.');
        }

        const auth = firebase.auth();
        const db = firebase.firestore();

        // DOM Elements
        const loading = document.getElementById('loading');
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

        // Hide loading when page is ready
        window.addEventListener('load', () => {
            setTimeout(() => {
                loading.style.display = 'none';
                loginPage.style.display = 'flex';
            }, 1000);
        });

        // Google Login
        loginBtn.addEventListener('click', () => {
            loading.style.display = 'flex';
            const provider = new firebase.auth.GoogleAuthProvider();
            
            auth.signInWithPopup(provider)
                .then((result) => {
                    currentUser = result.user;
                    setupUserProfile();
                    loading.style.display = 'none';
                    loginPage.style.display = 'none';
                    app.style.display = 'flex';
                    
                    generateUserId();
                    loadContacts();
                })
                .catch((error) => {
                    console.error('Login error:', error);
                    loading.style.display = 'none';
                    alert('Login gagal: ' + error.message);
                });
        });

        // ... (rest of the JavaScript code remains the same as previous version)
        // Logout, Add Friend, Send Message functions tetap sama

        // Setup user profile
        function setupUserProfile() {
            userAvatar.textContent = currentUser.displayName ? currentUser.displayName.charAt(0).toUpperCase() : 'U';
            userName.textContent = currentUser.displayName || 'User';
        }

        // Generate user ID
        function generateUserId() {
            db.collection('users').doc(currentUser.uid).get()
                .then((doc) => {
                    if (doc.exists) {
                        userData = doc.data();
                        userIdDisplay.textContent = userData.userId;
                    } else {
                        const userId = 'USER_' + Math.random().toString(36).substr(2, 9).toUpperCase();
                        userIdDisplay.textContent = userId;
                        
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
                contactsList.innerHTML = `
                    <div style="text-align: center; padding: 20px; color: var(--text-light);">
                        <i class="fas fa-users" style="font-size: 24px; margin-bottom: 10px;"></i>
                        <p>Belum ada kontak. Tambahkan teman dengan User ID mereka.</p>
                    </div>
                `;
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
            
            // Enable message input
            messageInput.disabled = false;
            sendBtn.disabled = false;
            messageInput.placeholder = "Ketik pesan...";
            
            // Update active state in contacts list
            document.querySelectorAll('.contact-item').forEach(item => {
                item.classList.remove('active');
                if (item.dataset.id === contact.userId) {
                    item.classList.add('active');
                }
            });
            
            loadMessages(contact.userId);
        }

        // Load messages
        function loadMessages(contactId) {
            chatMessages.innerHTML = '';
            
            const chatId = [currentUser.uid, contactId].sort().join('_');
            
            db.collection('chats').doc(chatId).collection('messages')
                .orderBy('timestamp', 'asc')
                .onSnapshot((snapshot) => {
                    chatMessages.innerHTML = '';
                    snapshot.forEach((doc) => {
                        const message = doc.data();
                        addMessageToChat(message.text, message.timestamp.toDate(), message.senderId === currentUser.uid ? 'me' : 'other');
                    });
                    
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
            const chatId = [currentUser.uid, selectedContact.uid].sort().join('_');
            
            db.collection('chats').doc(chatId).collection('messages').add({
                text: messageText,
                senderId: currentUser.uid,
                senderName: currentUser.displayName,
                timestamp: timestamp,
                read: false
            })
            .then(() => {
                messageInput.value = '';
            })
            .catch((error) => {
                console.error('Error sending message:', error);
                alert('Gagal mengirim pesan. Silakan coba lagi.');
            });
        }

        // Add friend
        function addFriend(friendId) {
            db.collection('users').where('userId', '==', friendId).get()
                .then((snapshot) => {
                    if (snapshot.empty) {
                        alert('User dengan ID tersebut tidak ditemukan');
                        return;
                    }
                    
                    snapshot.forEach((doc) => {
                        const friendData = doc.data();
                        
                        if (contacts.some(contact => contact.userId === friendId)) {
                            alert('Teman sudah ada di daftar kontak');
                            return;
                        }
                        
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
                loading.style.display = 'none';
                loginPage.style.display = 'none';
                app.style.display = 'flex';
                generateUserId();
                loadContacts();
            } else {
                app.style.display = 'none';
                loading.style.display = 'none';
                loginPage.style.display = 'flex';
            }
        });

        // Close modal when clicking outside
        window.addEventListener('click', (e) => {
            if (e.target === addFriendModal) {
                addFriendModal.style.display = 'none';
            }
        });

        // Error handling for Firebase
        db.enablePersistence().catch((err) => {
            console.log('Firebase persistence failed: ', err);
        });
    </script>
</body>
</html>
