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
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-storage.js"></script>
    <style>
        :root {
            --primary-green: #128C7E;
            --light-green: #25D366;
            --dark-green: #075E54;
            --primary-blue: #34B7F1;
            --light-blue: #6BCFFF;
            --yellow-color: #FFD700;
            --light-yellow: #FFF9C4;
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
        
        /* Loading */
        .loading {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(255,255,255,0.9);
            z-index: 1000;
            justify-content: center;
            align-items: center;
            flex-direction: column;
        }
        
        .loading-spinner {
            width: 40px;
            height: 40px;
            border: 4px solid #f3f3f3;
            border-top: 4px solid var(--primary-green);
            border-radius: 50%;
            animation: spin 1s linear infinite;
        }
        
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        
        /* Error Message */
        .error-message {
            display: none;
            background: #ffebee;
            color: #c62828;
            padding: 15px;
            border-radius: 10px;
            margin: 20px 0;
            text-align: center;
            border: 1px solid #ffcdd2;
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
            font-size: 24px;
        }
        
        .login-container p {
            color: var(--text-light);
            margin-bottom: 30px;
            line-height: 1.5;
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
            width: 100%;
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
            position: relative;
        }
        
        .mobile-back-btn {
            display: none;
            background: none;
            border: none;
            color: white;
            font-size: 18px;
            padding: 8px;
            margin-right: 10px;
            cursor: pointer;
            border-radius: 50%;
            transition: background-color 0.2s;
        }
        
        .mobile-back-btn:hover {
            background-color: rgba(255,255,255,0.1);
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
            border: 2px solid white;
            background-size: cover;
            background-position: center;
        }
        
        .user-details h3 {
            font-size: 16px;
            margin-bottom: 2px;
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
            padding: 8px;
            border-radius: 50%;
            transition: background-color 0.2s;
        }
        
        .header-actions i:hover {
            background-color: rgba(255,255,255,0.1);
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
            min-width: 300px;
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
            font-size: 14px;
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
            transition: all 0.2s;
        }
        
        .tab.active {
            color: var(--primary-green);
            border-bottom: 2px solid var(--primary-green);
        }
        
        .tab:hover {
            background-color: #f9f9f9;
        }
        
        .contacts-list {
            flex: 1;
            overflow-y: auto;
        }
        
        /* Chat List Item */
        .chat-item {
            display: flex;
            padding: 15px;
            border-bottom: 1px solid var(--border-light);
            cursor: pointer;
            transition: background-color 0.2s;
            background: linear-gradient(135deg, var(--light-blue), var(--yellow-color));
            margin: 5px;
            border-radius: 10px;
        }
        
        .chat-item:hover {
            background: linear-gradient(135deg, var(--primary-blue), var(--yellow-color));
            transform: translateY(-2px);
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
        }
        
        .chat-item.active {
            background: linear-gradient(135deg, var(--primary-blue), var(--yellow-color));
            border: 2px solid var(--primary-green);
        }
        
        .chat-avatar {
            width: 50px;
            height: 50px;
            border-radius: 50%;
            background-color: white;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            color: var(--primary-blue);
            margin-right: 15px;
            font-size: 18px;
            border: 2px solid white;
            background-size: cover;
            background-position: center;
        }
        
        .chat-info {
            flex: 1;
            display: flex;
            flex-direction: column;
            justify-content: center;
        }
        
        .chat-name {
            font-weight: 600;
            margin-bottom: 5px;
            font-size: 16px;
            color: var(--text-dark);
        }
        
        .chat-last-message {
            font-size: 13px;
            color: var(--text-dark);
            opacity: 0.8;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }
        
        .chat-time {
            font-size: 11px;
            color: var(--text-dark);
            opacity: 0.6;
            margin-top: 2px;
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
            border: 2px solid var(--light-blue);
            background-size: cover;
            background-position: center;
        }
        
        .chat-header-info {
            flex: 1;
        }
        
        .chat-header-name {
            font-weight: 500;
            font-size: 16px;
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
            box-shadow: 0 1px 2px rgba(0,0,0,0.1);
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
        
        .message-image {
            max-width: 100%;
            border-radius: 10px;
            margin-top: 5px;
            cursor: pointer;
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
            border-top: 1px solid var(--border-light);
        }
        
        .chat-input {
            flex: 1;
            padding: 12px 15px;
            border: 1px solid var(--border-light);
            border-radius: 20px;
            outline: none;
            margin: 0 10px;
            font-size: 14px;
            transition: border-color 0.2s;
        }
        
        .chat-input:focus {
            border-color: var(--primary-green);
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
            transition: background-color 0.2s;
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
        
        /* File Input */
        .file-input {
            display: none;
        }
        
        /* Modals */
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
            animation: modalAppear 0.3s ease-out;
        }
        
        @keyframes modalAppear {
            from {
                opacity: 0;
                transform: scale(0.9);
            }
            to {
                opacity: 1;
                transform: scale(1);
            }
        }
        
        .modal-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 15px;
            padding-bottom: 10px;
            border-bottom: 1px solid var(--border-light);
        }
        
        .modal-title {
            font-weight: 500;
            color: var(--text-dark);
            font-size: 18px;
        }
        
        .close-modal {
            background: none;
            border: none;
            font-size: 20px;
            cursor: pointer;
            color: var(--text-light);
            padding: 5px;
            border-radius: 50%;
            transition: background-color 0.2s;
        }
        
        .close-modal:hover {
            background-color: #f0f0f0;
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
            font-size: 14px;
            transition: border-color 0.2s;
        }
        
        .modal-input:focus {
            border-color: var(--primary-green);
            outline: none;
        }
        
        .modal-btn {
            background-color: var(--primary-green);
            color: white;
            border: none;
            padding: 12px 15px;
            border-radius: 5px;
            cursor: pointer;
            width: 100%;
            font-size: 14px;
            font-weight: 500;
            transition: background-color 0.3s;
        }
        
        .modal-btn:hover {
            background-color: var(--dark-green);
        }
        
        /* Profile Settings */
        .profile-avatar {
            width: 100px;
            height: 100px;
            border-radius: 50%;
            background-color: var(--light-blue);
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            color: white;
            margin: 0 auto 20px;
            font-size: 36px;
            cursor: pointer;
            background-size: cover;
            background-position: center;
            border: 3px solid var(--primary-green);
        }
        
        .profile-avatar:hover::after {
            content: 'Ganti Foto';
            position: absolute;
            background: rgba(0,0,0,0.7);
            color: white;
            padding: 5px 10px;
            border-radius: 5px;
            font-size: 12px;
        }
        
        /* User ID Display */
        .user-id-display {
            background-color: #f0f8ff;
            border: 1px dashed var(--primary-blue);
            border-radius: 5px;
            padding: 15px;
            margin-top: 15px;
            text-align: center;
            font-size: 14px;
        }
        
        .user-id {
            font-weight: bold;
            color: var(--primary-blue);
            margin-top: 5px;
            font-size: 16px;
            padding: 8px;
            background: white;
            border-radius: 5px;
            border: 1px solid var(--light-blue);
        }
        
        /* Image Preview */
        .image-preview {
            max-width: 200px;
            max-height: 200px;
            margin: 10px 0;
            border-radius: 10px;
            display: none;
        }
        
        /* Chat Loading States */
        .chat-loading {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 200px;
            flex-direction: column;
            color: var(--text-light);
        }
        
        .chat-empty {
            text-align: center;
            padding: 40px 20px;
            color: var(--text-light);
        }
        
        .chat-empty i {
            font-size: 48px;
            margin-bottom: 20px;
            opacity: 0.5;
        }
        
        /* Responsive */
        @media (max-width: 768px) {
            .mobile-back-btn {
                display: block;
            }
            
            .sidebar {
                width: 100%;
            }
            
            .app-content {
                flex-direction: column;
            }
            
            .chat-area {
                display: none;
                position: fixed;
                top: 0;
                left: 0;
                width: 100%;
                height: 100%;
                z-index: 100;
                transform: translateX(100%);
                transition: transform 0.3s ease;
            }
            
            .chat-area.active {
                display: flex;
                transform: translateX(0);
            }
            
            .chat-avatar {
                width: 45px;
                height: 45px;
            }
            
            .chat-name {
                font-size: 15px;
            }
        }
        
        /* Scrollbar Styling */
        ::-webkit-scrollbar {
            width: 6px;
        }
        
        ::-webkit-scrollbar-track {
            background: #f1f1f1;
        }
        
        ::-webkit-scrollbar-thumb {
            background: #c1c1c1;
            border-radius: 3px;
        }
        
        ::-webkit-scrollbar-thumb:hover {
            background: #a8a8a8;
        }
    </style>
</head>
<body>
    <!-- Loading Indicator -->
    <div class="loading" id="loading">
        <div class="loading-spinner"></div>
        <p style="margin-top: 15px; color: var(--primary-green);">Menyiapkan LeafSays...</p>
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
            
            <!-- Error Message -->
            <div class="error-message" id="error-message">
                <i class="fas fa-exclamation-triangle"></i>
                <span id="error-text">Terjadi kesalahan</span>
            </div>
            
            <button id="login-btn">
                <i class="fab fa-google"></i> Login dengan Google
            </button>
        </div>
    </div>
    
    <!-- Main App -->
    <div id="app">
        <!-- Header -->
        <div class="app-header">
            <button class="mobile-back-btn" id="mobile-back-btn" style="display: none;">
                <i class="fas fa-arrow-left"></i>
            </button>
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
                <i class="fas fa-user-plus" id="add-friend-btn" title="Tambah Teman"></i>
                <i class="fas fa-cog" id="settings-btn" title="Pengaturan"></i>
                <i class="fas fa-sign-out-alt" id="logout-btn" title="Logout"></i>
            </div>
        </div>
        
        <!-- Main Content -->
        <div class="app-content">
            <!-- Sidebar -->
            <div class="sidebar">
                <div class="sidebar-header">
                    <div class="search-container">
                        <i class="fas fa-search"></i>
                        <input type="text" placeholder="Cari atau mulai chat baru" id="search-contacts">
                    </div>
                </div>
                
                <div class="sidebar-tabs">
                    <div class="tab active" id="tab-chats">Chats</div>
                    <div class="tab" id="tab-contacts">Kontak</div>
                </div>
                
                <div class="contacts-list" id="contacts-list">
                    <!-- Chat list will be loaded here -->
                </div>
            </div>
            
            <!-- Chat Area -->
            <div class="chat-area">
                <div class="chat-header">
                    <div class="chat-header-avatar" id="chat-header-avatar">L</div>
                    <div class="chat-header-info">
                        <div class="chat-header-name" id="chat-header-name">LeafSays Chat</div>
                        <div class="chat-header-status" id="chat-header-status">Pilih chat untuk memulai percakapan</div>
                    </div>
                </div>
                
                <div class="chat-messages" id="chat-messages">
                    <div class="message message-received">
                        <div class="message-text">Selamat datang di LeafSays! 🌿</div>
                        <div class="message-text" style="margin-top: 5px;">Pilih chat dari daftar untuk memulai percakapan real-time.</div>
                        <div class="message-time">Sekarang</div>
                    </div>
                </div>
                
                <div class="chat-input-container">
                    <button class="chat-action-btn" id="attach-btn">
                        <i class="fas fa-paperclip"></i>
                    </button>
                    <input type="file" class="file-input" id="image-input" accept="image/*">
                    <input type="text" class="chat-input" placeholder="Pilih chat terlebih dahulu" id="message-input" disabled>
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

    <!-- Profile Settings Modal -->
    <div class="modal" id="profile-modal">
        <div class="modal-content">
            <div class="modal-header">
                <h3 class="modal-title">Pengaturan Profil</h3>
                <button class="close-modal">&times;</button>
            </div>
            <div class="modal-body">
                <div class="profile-avatar" id="profile-avatar-preview">
                    U
                </div>
                <input type="file" id="avatar-input" accept="image/*" style="display: none;">
                <button class="modal-btn" onclick="document.getElementById('avatar-input').click()">
                    <i class="fas fa-camera"></i> Ganti Foto Profil
                </button>
                
                <div style="margin: 20px 0;">
                    <label style="display: block; margin-bottom: 5px; font-weight: 500;">Nama Panggilan:</label>
                    <input type="text" class="modal-input" id="profile-name-input" placeholder="Masukkan nama panggilan">
                </div>
                
                <button class="modal-btn" id="save-profile-btn">
                    <i class="fas fa-save"></i> Simpan Perubahan
                </button>
            </div>
        </div>
    </div>

    <script>
        // Firebase configuration
        const firebaseConfig = {
            apiKey: "AIzaSyBjPJj73wSIg_0ffEpTS4Z7ecBWJs6xRvI",
            authDomain: "leafsays-a8c84.firebaseapp.com",
            projectId: "leafsays-a8c84",
            storageBucket: "leafsays-a8c84.firebasestorage.app",
            messagingSenderId: "863298565588",
            appId: "1:863298565588:web:f379e717f118c9ed41d241"
        };

        // DOM Elements
        const loading = document.getElementById('loading');
        const errorMessage = document.getElementById('error-message');
        const errorText = document.getElementById('error-text');
        const loginPage = document.getElementById('login-page');
        const app = document.getElementById('app');
        const loginBtn = document.getElementById('login-btn');
        const logoutBtn = document.getElementById('logout-btn');
        const addFriendBtn = document.getElementById('add-friend-btn');
        const settingsBtn = document.getElementById('settings-btn');
        const mobileBackBtn = document.getElementById('mobile-back-btn');
        const addFriendModal = document.getElementById('add-friend-modal');
        const profileModal = document.getElementById('profile-modal');
        const closeModals = document.querySelectorAll('.close-modal');
        const addFriendConfirm = document.getElementById('add-friend-confirm');
        const friendIdInput = document.getElementById('friend-id-input');
        const userIdDisplay = document.getElementById('user-id-display');
        const userAvatar = document.getElementById('user-avatar');
        const userName = document.getElementById('user-name');
        const contactsList = document.getElementById('contacts-list');
        const chatMessages = document.getElementById('chat-messages');
        const messageInput = document.getElementById('message-input');
        const sendBtn = document.getElementById('send-btn');
        const attachBtn = document.getElementById('attach-btn');
        const imageInput = document.getElementById('image-input');
        const chatHeaderName = document.getElementById('chat-header-name');
        const chatHeaderAvatar = document.getElementById('chat-header-avatar');
        const chatHeaderStatus = document.getElementById('chat-header-status');
        const profileAvatarPreview = document.getElementById('profile-avatar-preview');
        const avatarInput = document.getElementById('avatar-input');
        const profileNameInput = document.getElementById('profile-name-input');
        const saveProfileBtn = document.getElementById('save-profile-btn');
        const searchContacts = document.getElementById('search-contacts');
        const tabChats = document.getElementById('tab-chats');
        const tabContacts = document.getElementById('tab-contacts');

        // Global variables
        let currentUser = null;
        let selectedChat = null;
        let contacts = [];
        let chats = [];
        let userData = null;
        let auth, db, storage;
        let currentTab = 'chats';

        // Initialize Firebase
        function initializeFirebase() {
            try {
                if (!firebase.apps.length) {
                    firebase.initializeApp(firebaseConfig);
                }
                auth = firebase.auth();
                db = firebase.firestore();
                storage = firebase.storage();
                
                console.log('Firebase initialized successfully');
                hideError();
                return true;
            } catch (error) {
                console.error('Firebase initialization error:', error);
                showError('Gagal menginisialisasi Firebase: ' + error.message);
                return false;
            }
        }

        // Show error message
        function showError(message) {
            errorText.textContent = message;
            errorMessage.style.display = 'block';
        }

        // Hide error message
        function hideError() {
            errorMessage.style.display = 'none';
        }

        // Initialize when page loads
        document.addEventListener('DOMContentLoaded', function() {
            loading.style.display = 'flex';
            
            setTimeout(() => {
                if (initializeFirebase()) {
                    loading.style.display = 'none';
                    loginPage.style.display = 'flex';
                } else {
                    loading.style.display = 'none';
                }
            }, 1000);
        });

        // Event Listeners
        loginBtn.addEventListener('click', handleLogin);
        logoutBtn.addEventListener('click', handleLogout);
        addFriendBtn.addEventListener('click', () => addFriendModal.style.display = 'flex');
        settingsBtn.addEventListener('click', () => profileModal.style.display = 'flex');
        mobileBackBtn.addEventListener('click', handleMobileBack);
        addFriendConfirm.addEventListener('click', handleAddFriend);
        sendBtn.addEventListener('click', sendMessage);
        attachBtn.addEventListener('click', () => imageInput.click());
        imageInput.addEventListener('change', handleImageUpload);
        saveProfileBtn.addEventListener('click', saveProfile);
        searchContacts.addEventListener('input', filterContacts);
        
        tabChats.addEventListener('click', () => switchTab('chats'));
        tabContacts.addEventListener('click', () => switchTab('contacts'));

        // Close modals
        closeModals.forEach(btn => {
            btn.addEventListener('click', function() {
                this.closest('.modal').style.display = 'none';
            });
        });

        // Close modals when clicking outside
        window.addEventListener('click', (e) => {
            if (e.target.classList.contains('modal')) {
                e.target.style.display = 'none';
            }
        });

        // Message input enter key
        messageInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter') {
                sendMessage();
            }
        });

        // Avatar change
        avatarInput.addEventListener('change', handleAvatarChange);

        // Functions
        function handleLogin() {
            if (!auth) {
                showError('Firebase belum terinisialisasi. Refresh halaman.');
                return;
            }

            loading.style.display = 'flex';
            hideError();
            
            const provider = new firebase.auth.GoogleAuthProvider();
            provider.addScope('profile');
            provider.addScope('email');
            
            auth.signInWithPopup(provider)
                .then((result) => {
                    currentUser = result.user;
                    setupUserProfile();
                    loading.style.display = 'none';
                    loginPage.style.display = 'none';
                    app.style.display = 'flex';
                    
                    generateUserId();
                    loadContacts();
                    loadChats();
                })
                .catch((error) => {
                    loading.style.display = 'none';
                    console.error('Login error:', error);
                    showError('Login gagal: ' + error.message);
                });
        }

        function handleLogout() {
            if (auth) {
                auth.signOut();
            }
        }

        function handleMobileBack() {
            document.querySelector('.sidebar').style.display = 'flex';
            document.querySelector('.chat-area').classList.remove('active');
            mobileBackBtn.style.display = 'none';
        }

        function setupUserProfile() {
            if (currentUser && currentUser.displayName) {
                userAvatar.textContent = currentUser.displayName.charAt(0).toUpperCase();
                userName.textContent = currentUser.displayName;
                profileNameInput.value = currentUser.displayName;
            }
            
            // Load user profile data
            loadUserProfile();
        }

        function loadUserProfile() {
            if (!db || !currentUser) return;
            
            db.collection('users').doc(currentUser.uid).get()
                .then((doc) => {
                    if (doc.exists) {
                        const userData = doc.data();
                        if (userData.photoURL) {
                            userAvatar.style.backgroundImage = `url(${userData.photoURL})`;
                            userAvatar.textContent = '';
                            profileAvatarPreview.style.backgroundImage = `url(${userData.photoURL})`;
                            profileAvatarPreview.textContent = '';
                        }
                        if (userData.displayName) {
                            profileNameInput.value = userData.displayName;
                        }
                    }
                })
                .catch(error => {
                    console.error('Error loading user profile:', error);
                });
        }

        function generateUserId() {
            if (!db || !currentUser) return;
            
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

        function loadContacts() {
            if (!db || !currentUser) return;
            
            db.collection('users').doc(currentUser.uid).collection('contacts')
                .onSnapshot((snapshot) => {
                    contacts = [];
                    snapshot.forEach((doc) => {
                        const contact = doc.data();
                        contacts.push(contact);
                    });
                    renderContactList();
                }, (error) => {
                    console.error('Error loading contacts:', error);
                });
        }

        function loadChats() {
            if (!db || !currentUser) return;
            
            // Load actual chat conversations from Firestore
            db.collection('users').doc(currentUser.uid).collection('contacts')
                .onSnapshot((snapshot) => {
                    chats = [];
                    snapshot.forEach((doc) => {
                        const contact = doc.data();
                        // For demo, we'll create a default chat
                        chats.push({
                            ...contact,
                            lastMessage: 'Halo! Mulai percakapan...',
                            lastMessageTime: new Date()
                        });
                    });
                    renderChatList();
                });
        }

        function renderContactList() {
            if (currentTab !== 'contacts') return;
            
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
            
            const filteredContacts = filterContactsBySearch(contacts);
            
            filteredContacts.forEach(contact => {
                const contactItem = document.createElement('div');
                contactItem.className = 'chat-item';
                contactItem.innerHTML = `
                    <div class="chat-avatar" style="${contact.photoURL ? `background-image: url(${contact.photoURL})` : ''}">
                        ${!contact.photoURL ? (contact.displayName ? contact.displayName.charAt(0).toUpperCase() : 'F') : ''}
                    </div>
                    <div class="chat-info">
                        <div class="chat-name">${contact.displayName || 'Friend'}</div>
                        <div class="chat-last-message">${contact.userId}</div>
                        <div class="chat-time">Kontak</div>
                    </div>
                `;
                
                contactItem.addEventListener('click', () => {
                    selectChat(contact);
                });
                
                contactsList.appendChild(contactItem);
            });
        }

        function renderChatList() {
            if (currentTab !== 'chats') return;
            
            contactsList.innerHTML = '';
            
            if (chats.length === 0) {
                contactsList.innerHTML = `
                    <div style="text-align: center; padding: 20px; color: var(--text-light);">
                        <i class="fas fa-comments" style="font-size: 24px; margin-bottom: 10px;"></i>
                        <p>Belum ada percakapan. Tambahkan teman untuk memulai chat.</p>
                    </div>
                `;
                return;
            }
            
            const filteredChats = filterContactsBySearch(chats);
            
            filteredChats.forEach(chat => {
                const chatItem = document.createElement('div');
                chatItem.className = 'chat-item';
                if (selectedChat && selectedChat.userId === chat.userId) {
                    chatItem.classList.add('active');
                }
                
                chatItem.innerHTML = `
                    <div class="chat-avatar" style="${chat.photoURL ? `background-image: url(${chat.photoURL})` : ''}">
                        ${!chat.photoURL ? (chat.displayName ? chat.displayName.charAt(0).toUpperCase() : 'F') : ''}
                    </div>
                    <div class="chat-info">
                        <div class="chat-name">${chat.displayName || 'Friend'}</div>
                        <div class="chat-last-message">${chat.lastMessage}</div>
                        <div class="chat-time">${formatTime(chat.lastMessageTime)}</div>
                    </div>
                `;
                
                chatItem.addEventListener('click', () => {
                    selectChat(chat);
                });
                
                contactsList.appendChild(chatItem);
            });
        }

        function filterContactsBySearch(items) {
            const searchTerm = searchContacts.value.toLowerCase();
            if (!searchTerm) return items;
            
            return items.filter(item => 
                (item.displayName && item.displayName.toLowerCase().includes(searchTerm)) ||
                (item.userId && item.userId.toLowerCase().includes(searchTerm))
            );
        }

        function filterContacts() {
            if (currentTab === 'chats') {
                renderChatList();
            } else {
                renderContactList();
            }
        }

        function switchTab(tab) {
            currentTab = tab;
            tabChats.classList.toggle('active', tab === 'chats');
            tabContacts.classList.toggle('active', tab === 'contacts');
            
            if (tab === 'chats') {
                renderChatList();
            } else {
                renderContactList();
            }
        }

        function selectChat(chat) {
            console.log('Chat selected:', chat);
            
            selectedChat = chat;
            chatHeaderName.textContent = chat.displayName || 'Friend';
            chatHeaderAvatar.textContent = chat.displayName ? chat.displayName.charAt(0).toUpperCase() : 'F';
            chatHeaderAvatar.style.backgroundImage = chat.photoURL ? `url(${chat.photoURL})` : '';
            if (chat.photoURL) chatHeaderAvatar.textContent = '';
            chatHeaderStatus.textContent = 'Online';
            
            // Enable message input
            messageInput.disabled = false;
            sendBtn.disabled = false;
            messageInput.placeholder = "Ketik pesan...";
            messageInput.focus();
            
            // Update mobile view
            if (window.innerWidth <= 768) {
                document.querySelector('.sidebar').style.display = 'none';
                document.querySelector('.chat-area').classList.add('active');
                mobileBackBtn.style.display = 'block';
            }
            
            // Reload lists to update active state
            if (currentTab === 'chats') {
                renderChatList();
            } else {
                renderContactList();
            }
            
            loadMessages(chat.userId);
        }

        function loadMessages(contactId) {
            if (!db || !currentUser || !contactId) {
                console.error('Cannot load messages: missing db, user, or contactId');
                return;
            }
            
            console.log('Loading messages for:', contactId);
            
            // Clear chat messages dan tampilkan loading
            chatMessages.innerHTML = `
                <div class="chat-loading">
                    <div class="loading-spinner"></div>
                    <p>Memuat percakapan...</p>
                </div>
            `;
            
            const chatId = [currentUser.uid, contactId].sort().join('_');
            console.log('Chat ID:', chatId);
            
            // Listen untuk messages real-time
            db.collection('chats').doc(chatId).collection('messages')
                .orderBy('timestamp', 'asc')
                .onSnapshot((snapshot) => {
                    console.log('Messages snapshot:', snapshot.size, 'messages');
                    
                    chatMessages.innerHTML = '';
                    
                    if (snapshot.empty) {
                        // Jika belum ada pesan, tampilkan pesan default
                        chatMessages.innerHTML = `
                            <div class="chat-empty">
                                <i class="fas fa-comments"></i>
                                <h3 style="margin-bottom: 10px;">Mulai Percakapan</h3>
                                <p>Kirim pesan pertama untuk memulai chat dengan ${selectedChat?.displayName || 'teman'}!</p>
                            </div>
                        `;
                        return;
                    }
                    
                    snapshot.forEach((doc) => {
                        const message = doc.data();
                        addMessageToChat(message);
                    });
                    
                    // Scroll ke bottom
                    setTimeout(() => {
                        chatMessages.scrollTop = chatMessages.scrollHeight;
                    }, 100);
                }, (error) => {
                    console.error('Error loading messages:', error);
                    chatMessages.innerHTML = `
                        <div style="text-align: center; padding: 20px; color: #c62828;">
                            <i class="fas fa-exclamation-triangle"></i>
                            <p>Gagal memuat pesan. Silakan refresh halaman.</p>
                        </div>
                    `;
                });
        }

        function addMessageToChat(message) {
            const messageDiv = document.createElement('div');
            const isSent = message.senderId === currentUser.uid;
            messageDiv.className = `message ${isSent ? 'message-sent' : 'message-received'}`;
            
            const timeString = formatTime(message.timestamp.toDate());
            
            let messageContent = '';
            if (message.type === 'image') {
                messageContent = `<img src="${message.imageUrl}" class="message-image" onclick="openImage('${message.imageUrl}')">`;
            } else {
                messageContent = `<div class="message-text">${message.text}</div>`;
            }
            
            messageDiv.innerHTML = `
                ${messageContent}
                <div class="message-time">${timeString}</div>
            `;
            
            chatMessages.appendChild(messageDiv);
        }

        function sendMessage() {
            const messageText = messageInput.value.trim();
            if (!messageText || !selectedChat || !db) {
                alert('Pilih chat terlebih dahulu dan ketik pesan');
                return;
            }
            
            console.log('Sending message to:', selectedChat.userId);
            
            const timestamp = new Date();
            const chatId = [currentUser.uid, selectedChat.uid].sort().join('_');
            
            // Tambahkan pesan ke Firestore
            db.collection('chats').doc(chatId).collection('messages').add({
                text: messageText,
                senderId: currentUser.uid,
                senderName: currentUser.displayName || 'User',
                timestamp: timestamp,
                type: 'text',
                read: false
            })
            .then(() => {
                console.log('Message sent successfully');
                messageInput.value = '';
                
                // Update last message di chat list
                updateLastMessage(selectedChat.userId, messageText, timestamp);
                
                // Auto scroll ke bottom
                setTimeout(() => {
                    chatMessages.scrollTop = chatMessages.scrollHeight;
                }, 100);
            })
            .catch((error) => {
                console.error('Error sending message:', error);
                alert('Gagal mengirim pesan. Silakan coba lagi.');
            });
        }

        function handleImageUpload(event) {
            const file = event.target.files[0];
            if (!file || !selectedChat) return;
            
            // Check file size (50MB limit)
            if (file.size > 50 * 1024 * 1024) {
                alert('Ukuran file maksimal 50MB');
                return;
            }
            
            // Check if it's an image
            if (!file.type.startsWith('image/')) {
                alert('Hanya file gambar yang diizinkan');
                return;
            }
            
            loading.style.display = 'flex';
            
            const storageRef = storage.ref();
            const imageRef = storageRef.child(`chat_images/${currentUser.uid}/${Date.now()}_${file.name}`);
            
            imageRef.put(file)
                .then(snapshot => snapshot.ref.getDownloadURL())
                .then(imageUrl => {
                    const timestamp = new Date();
                    const chatId = [currentUser.uid, selectedChat.uid].sort().join('_');
                    
                    return db.collection('chats').doc(chatId).collection('messages').add({
                        imageUrl: imageUrl,
                        senderId: currentUser.uid,
                        senderName: currentUser.displayName,
                        timestamp: timestamp,
                        type: 'image',
                        read: false
                    });
                })
                .then(() => {
                    loading.style.display = 'none';
                    imageInput.value = '';
                    updateLastMessage(selectedChat.userId, '📷 Gambar', new Date());
                })
                .catch(error => {
                    loading.style.display = 'none';
                    console.error('Error uploading image:', error);
                    alert('Gagal mengupload gambar. Silakan coba lagi.');
                });
        }

        function handleAddFriend() {
            const friendId = friendIdInput.value.trim();
            if (friendId) {
                addFriend(friendId);
                friendIdInput.value = '';
                addFriendModal.style.display = 'none';
            } else {
                alert('Masukkan User ID yang valid');
            }
        }

        function addFriend(friendId) {
            if (!db) return;
            
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
                        
                        if (friendData.uid === currentUser.uid) {
                            alert('Tidak bisa menambahkan diri sendiri');
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
                            // Add to chats list
                            chats.push({
                                ...friendData,
                                lastMessage: 'Halo! Mulai percakapan...',
                                lastMessageTime: new Date()
                            });
                            renderChatList();
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

        function handleAvatarChange(event) {
            const file = event.target.files[0];
            if (!file) return;
            
            if (!file.type.startsWith('image/')) {
                alert('Hanya file gambar yang diizinkan');
                return;
            }
            
            loading.style.display = 'flex';
            
            const storageRef = storage.ref();
            const avatarRef = storageRef.child(`avatars/${currentUser.uid}/avatar.jpg`);
            
            avatarRef.put(file)
                .then(snapshot => snapshot.ref.getDownloadURL())
                .then(avatarUrl => {
                    return db.collection('users').doc(currentUser.uid).update({
                        photoURL: avatarUrl
                    });
                })
                .then(() => {
                    loading.style.display = 'none';
                    userAvatar.style.backgroundImage = `url(${avatarUrl})`;
                    userAvatar.textContent = '';
                    profileAvatarPreview.style.backgroundImage = `url(${avatarUrl})`;
                    profileAvatarPreview.textContent = '';
                    alert('Foto profil berhasil diubah!');
                })
                .catch(error => {
                    loading.style.display = 'none';
                    console.error('Error uploading avatar:', error);
                    alert('Gagal mengubah foto profil. Silakan coba lagi.');
                });
        }

        function saveProfile() {
            const newName = profileNameInput.value.trim();
            if (!newName) {
                alert('Masukkan nama panggilan');
                return;
            }
            
            db.collection('users').doc(currentUser.uid).update({
                displayName: newName
            })
            .then(() => {
                userName.textContent = newName;
                userAvatar.textContent = newName.charAt(0).toUpperCase();
                alert('Profil berhasil diperbarui!');
                profileModal.style.display = 'none';
            })
            .catch(error => {
                console.error('Error updating profile:', error);
                alert('Gagal memperbarui profil. Silakan coba lagi.');
            });
        }

        function updateLastMessage(contactId, message, timestamp) {
            const chatIndex = chats.findIndex(chat => chat.userId === contactId);
            if (chatIndex !== -1) {
                chats[chatIndex].lastMessage = message;
                chats[chatIndex].lastMessageTime = timestamp;
                renderChatList();
            }
        }

        function formatTime(date) {
            return `${date.getHours()}:${date.getMinutes().toString().padStart(2, '0')}`;
        }

        function openImage(url) {
            window.open(url, '_blank');
        }

        // Debug helper untuk test chat functionality
        function testChatFunctionality() {
            console.log('=== TEST CHAT FUNCTIONALITY ===');
            console.log('Current User:', currentUser);
            console.log('Selected Chat:', selectedChat);
            console.log('Contacts:', contacts);
            console.log('Chats:', chats);
            console.log('Firebase DB:', db ? 'Connected' : 'Not connected');
        }

        // Expose test function ke global scope untuk debugging
        window.testChat = testChatFunctionality;

        // Auth state observer
        if (auth) {
            auth.onAuthStateChanged((user) => {
                if (user) {
                    currentUser = user;
                    setupUserProfile();
                    loginPage.style.display = 'none';
                    app.style.display = 'flex';
                    generateUserId();
                    loadContacts();
                    loadChats();
                } else {
                    app.style.display = 'none';
                    loginPage.style.display = 'flex';
                    contacts = [];
                    chats = [];
                    selectedChat = null;
                }
            });
        }
    </script>
</body>
</html>
