# google-oauth-starter
Complete Google OAuth 2.0 Authentication System with Auto-Setup
# 🔐 Google OAuth Authentication Starter Kit

[![Node.js](https://img.shields.io/badge/Node.js-18+-green.svg)](https://nodejs.org/)
[![Express](https://img.shields.io/badge/Express-4.x-blue.svg)](https://expressjs.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

A complete, production-ready Google OAuth 2.0 authentication system. Setup in 5 minutes!

## 🚀 Quick Start

### **Option 1: One-Click Setup (Recommended)**
```bash
# Clone the repository
git clone https://github.com/MohtadyDeveloper/google-oauth-starter.git
cd google-oauth-starter

# Run the setup wizard (guides you through Google Cloud setup)
npm run setup

# Start the application
npm start

Manual Setup
# 1. Clone
git clone https://github.com/MohtadyDeveloper/google-oauth-starter.git
cd google-oauth-starter

# 2. Install dependencies
npm install

# 3. Configure environment
cp .env.example .env
# Edit .env with your Google credentials

# 4. Start
npm start

📋 Prerequisites
Node.js (v18 or higher)

npm (comes with Node.js)

Google Account (for OAuth credentials)

🔧 Google OAuth Setup (One-Time)
Step 1: Create Google Cloud Project
Go to Google Cloud Console

Click "Create Project" → Name it (e.g., "OAuth Demo")

Click "Create"

Step 2: Configure OAuth Consent Screen
Navigate to APIs & Services → OAuth consent screen

Choose External user type → Click Create

Fill required fields:

App name: Google Auth Demo

User support email: Your email

Developer contact email: Your email

Click Save and Continue through all steps

Step 3: Create OAuth Credentials
Go to APIs & Services → Credentials

Click Create Credentials → OAuth 2.0 Client ID

Configure:

Application type: Web application

Name: Google Auth Demo

Authorized JavaScript origins: http://localhost:3000

Authorized redirect URIs: http://localhost:3000/auth/google/callback

Click Create

Copy Client ID and Client Secret

Step 4: Configure Environment

bash

# Copy the template
cp .env.example .env

# Edit .env file with your credentials
GOOGLE_CLIENT_ID=your_client_id_here
GOOGLE_CLIENT_SECRET=your_client_secret_here
bash

# Copy the template
cp .env.example .env

# Edit .env file with your credentials
GOOGLE_CLIENT_ID=your_client_id_here
GOOGLE_CLIENT_SECRET=your_client_secret_here

🎮 Usage
Start Development Server:
npm run dev
# Server runs at http://localhost:3000

Start Production Server:
bash
npm start

API Endpoints:
GET / - Home page with login

GET /auth/google - Initiate Google login

GET /api/user - Get current user info (JSON)

GET /logout - Logout user

📁 Project Structure

google-oauth-starter/
├── server.js                 # Main Express server
├── package.json             # Dependencies
├── .env.example             # Environment template
├── setup.js                 # Interactive setup wizard
├── public/                  # Frontend assets
│   ├── index.html
│   ├── style.css
│   └── app.js
├── README.md               # You are here
└── LICENSE                 # MIT License

🛠️ Features
✅ Complete OAuth 2.0 Implementation
✅ Interactive Setup Wizard
✅ Session Management
✅ User Profile Display
✅ Mobile-Responsive UI
✅ Environment Configuration
✅ Error Handling
✅ Health Checks
✅ Logout Functionality
✅ Team-Ready Documentation

🧪 Testing the Application
Open browser to http://localhost:3000

Click "Sign in with Google"

Select your Google account

Grant permissions (if prompted)

View your profile information

🔍 Troubleshooting
Common Issues:
"redirect_uri_mismatch" Error:

Ensure redirect URI in Google Cloud exactly matches:
http://localhost:3000/auth/google/callback

"Invalid Credentials" Error:

Double-check Client ID and Secret in .env file

Ensure no extra spaces or characters

Port 3000 Already in Use:

Change PORT in .env file

Update redirect URI in Google Cloud Console

Session Not Persisting:

Clear browser cookies

Try incognito/private mode

📞 Support
Check Server Logs in terminal

Verify Google Cloud Configuration

Clear Browser Cache & Cookies

Restart the Server

🤝 Contributing
Fork the repository

Create a feature branch (git checkout -b feature/AmazingFeature)

Commit changes (git commit -m 'Add AmazingFeature')

Push to branch (git push origin feature/AmazingFeature)

Open a Pull Request

📄 License
Distributed under the MIT License. See LICENSE for more information.

🙏 Acknowledgments
Passport.js for authentication middleware

Google OAuth 2.0

Express.js framework

Happy Coding! 🚀 If you found this useful, please give it a ⭐ on GitHub!

text


### **2. `package.json`**
```json
{
  "name": "google-oauth-starter",
  "version": "1.0.0",
  "description": "Complete Google OAuth 2.0 Authentication System",
  "main": "server.js",
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js",
    "setup": "node setup.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [
    "oauth",
    "google-auth",
    "authentication",
    "passport",
    "express"
  ],
  "author": "Your Name",
  "license": "MIT",
  "dependencies": {
    "express": "^4.18.2",
    "express-session": "^1.17.3",
    "passport": "^0.6.0",
    "passport-google-oauth20": "^2.0.0",
    "dotenv": "^16.0.3",
    "cors": "^2.8.5"
  },
  "devDependencies": {
    "nodemon": "^3.0.1"
  },
  "engines": {
    "node": ">=18.0.0"
  }
}
server.js (Main Backend)
javascript
const express = require('express');
const session = require('express-session');
const passport = require('passport');
const GoogleStrategy = require('passport-google-oauth20').Strategy;
const path = require('path');
require('dotenv').config();

const app = express();
const PORT = process.env.PORT || 3000;

// ======================
// 1. CONFIGURATION CHECK
// ======================
if (!process.env.GOOGLE_CLIENT_ID || !process.env.GOOGLE_CLIENT_SECRET) {
  console.error('\n⚠️  MISSING GOOGLE CREDENTIALS!');
  console.log('Please configure your .env file with:');
  console.log('GOOGLE_CLIENT_ID=your_client_id');
  console.log('GOOGLE_CLIENT_SECRET=your_client_secret');
  console.log('\nOr run: npm run setup');
  console.log('========================================\n');
  process.exit(1);
}

// ======================
// 2. MIDDLEWARE
// ======================
app.use(express.json());
app.use(express.static(path.join(__dirname, 'public')));
app.use(session({
  secret: process.env.SESSION_SECRET || 'default_session_secret_change_me',
  resave: false,
  saveUninitialized: false,
  cookie: { secure: false, maxAge: 24 * 60 * 60 * 1000 } // 24 hours
}));

// ======================
// 3. PASSPORT SETUP
// ======================
app.use(passport.initialize());
app.use(passport.session());

passport.use(new GoogleStrategy({
    clientID: process.env.GOOGLE_CLIENT_ID,
    clientSecret: process.env.GOOGLE_CLIENT_SECRET,
    callbackURL: `http://localhost:${PORT}/auth/google/callback`,
    scope: ['profile', 'email']
  },
  (accessToken, refreshToken, profile, done) => {
    console.log(`✅ User authenticated: ${profile.displayName}`);
    return done(null, profile);
  }
));

passport.serializeUser((user, done) => done(null, user));
passport.deserializeUser((user, done) => done(null, user));

// ======================
// 4. ROUTES
// ======================
app.get('/', (req, res) => {
  res.sendFile(path.join(__dirname, 'public', 'index.html'));
});

app.get('/auth/google',
  passport.authenticate('google', { scope: ['profile', 'email'] })
);

app.get('/auth/google/callback',
  passport.authenticate('google', { failureRedirect: '/' }),
  (req, res) => {
    res.redirect('/dashboard');
  }
);

app.get('/dashboard', (req, res) => {
  res.sendFile(path.join(__dirname, 'public', 'index.html'));
});

app.get('/api/user', (req, res) => {
  if (req.isAuthenticated()) {
    res.json({
      authenticated: true,
      user: {
        id: req.user.id,
        name: req.user.displayName,
        email: req.user.emails[0].value,
        photo: req.user.photos?.[0]?.value || '/default-avatar.png',
        provider: req.user.provider
      }
    });
  } else {
    res.json({ authenticated: false });
  }
});

app.get('/api/health', (req, res) => {
  res.json({
    status: 'healthy',
    service: 'Google OAuth Starter',
    timestamp: new Date().toISOString(),
    google_configured: !!(process.env.GOOGLE_CLIENT_ID && process.env.GOOGLE_CLIENT_SECRET),
    port: PORT
  });
});

app.get('/logout', (req, res) => {
  req.logout(() => {
    res.redirect('/');
  });
});

// ======================
// 5. START SERVER
// ======================
app.listen(PORT, () => {
  console.log('\n' + '='.repeat(50));
  console.log('🚀 GOOGLE OAUTH STARTER IS RUNNING!');
  console.log('='.repeat(50));
  console.log(`📍 Local:    http://localhost:${PORT}`);
  console.log(`📍 Network:  http://${getLocalIP()}:${PORT}`);
  console.log('='.repeat(50));
  console.log('📋 Open the URL above in your browser');
  console.log('='.repeat(50) + '\n');
});

function getLocalIP() {
  const interfaces = require('os').networkInterfaces();
  for (const name of Object.keys(interfaces)) {
    for (const iface of interfaces[name]) {
      if (iface.family === 'IPv4' && !iface.internal) {
        return iface.address;
      }
    }
  }
  return 'localhost';
}

setup.js (Interactive Setup Wizard)
const fs = require('fs');
const path = require('path');
const readline = require('readline');
const crypto = require('crypto');

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout
});

console.clear();
console.log('\x1b[36m%s\x1b[0m', '='.repeat(60));
console.log('\x1b[36m%s\x1b[0m', '🔧 GOOGLE OAUTH SETUP WIZARD');
console.log('\x1b[36m%s\x1b[0m', '='.repeat(60));

async function setup() {
  try {
    console.log('\n📦 Step 1: Installing dependencies...');
    const { execSync } = require('child_process');
    try {
      execSync('npm install', { stdio: 'inherit' });
      console.log('\x1b[32m%s\x1b[0m', '✅ Dependencies installed successfully!');
    } catch (error) {
      console.log('\x1b[33m%s\x1b[0m', '⚠️  Running npm install...');
    }

    console.log('\n⚙️  Step 2: Setting up environment configuration...');
    const envPath = path.join(__dirname, '.env');
    const envExamplePath = path.join(__dirname, '.env.example');
    
    if (!fs.existsSync(envPath)) {
      fs.copyFileSync(envExamplePath, envPath);
      const sessionSecret = crypto.randomBytes(32).toString('hex');
      let envContent = fs.readFileSync(envPath, 'utf8');
      envContent = envContent.replace('SESSION_SECRET=your_session_secret_here', 
                                     `SESSION_SECRET=${sessionSecret}`);
      fs.writeFileSync(envPath, envContent);
      console.log('\x1b[32m%s\x1b[0m', '✅ Created .env file with secure session secret');
    } else {
      console.log('\x1b[32m%s\x1b[0m', '✅ .env file already exists');
    }

    console.log('\n🔑 Step 3: Google OAuth Configuration');
    console.log('\nChoose an option:');
    console.log('1. I have Google OAuth credentials ready');
    console.log('2. I need to create Google OAuth credentials');
    console.log('3. Skip for now (configure manually later)');
    
    const choice = await askQuestion('\nEnter choice (1-3): ');
    
    if (choice === '1') {
      await configureCredentials();
    } else if (choice === '2') {
      showGoogleSetupGuide();
    }

    console.log('\n' + '='.repeat(60));
    console.log('\x1b[32m%s\x1b[0m', '🎉 SETUP COMPLETE!');
    console.log('='.repeat(60));
    console.log('\nNext steps:');
    console.log('\x1b[36m%s\x1b[0m', '1. Start the application:');
    console.log('   npm start');
    console.log('\x1b[36m%s\x1b[0m', '2. Open in browser:');
    console.log('   http://localhost:3000');
    
    if (choice !== '1') {
      console.log('\n\x1b[33m%s\x1b[0m', '⚠️  IMPORTANT: You need to configure Google OAuth:');
      console.log('   - Edit the .env file');
      console.log('   - Add GOOGLE_CLIENT_ID and GOOGLE_CLIENT_SECRET');
    }
    
    console.log('\n' + '='.repeat(60));
    
  } catch (error) {
    console.error('\x1b[31m%s\x1b[0m', '❌ Setup failed:', error.message);
  } finally {
    rl.close();
  }
}

async function configureCredentials() {
  console.log('\n' + '-'.repeat(40));
  console.log('Enter your Google OAuth credentials:');
  console.log('(Get them from: https://console.cloud.google.com)');
  console.log('-'.repeat(40));
  
  const clientId = await askQuestion('\nGoogle Client ID: ');
  const clientSecret = await askQuestion('Google Client Secret: ');
  
  let envContent = fs.readFileSync('.env', 'utf8');
  envContent = envContent.replace('GOOGLE_CLIENT_ID=your_client_id_here', 
                                 `GOOGLE_CLIENT_ID=${clientId}`);
  envContent = envContent.replace('GOOGLE_CLIENT_SECRET=your_client_secret_here', 
                                 `GOOGLE_CLIENT_SECRET=${clientSecret}`);
  
  fs.writeFileSync('.env', envContent);
  console.log('\x1b[32m%s\x1b[0m', '✅ Google credentials saved to .env file');
}

function showGoogleSetupGuide() {
  console.log('\n' + '='.repeat(60));
  console.log('\x1b[36m%s\x1b[0m', '📋 GOOGLE CLOUD CONFIGURATION GUIDE');
  console.log('='.repeat(60));
  console.log('\n1. Visit: \x1b[34mhttps://console.cloud.google.com\x1b[0m');
  console.log('2. Create a new project or select existing');
  console.log('3. Go to: APIs & Services → Credentials');
  console.log('4. Click "Create Credentials" → "OAuth 2.0 Client ID"');
  console.log('5. Configure:');
  console.log('   - Application type: Web application');
  console.log('   - Name: Google Auth Demo');
  console.log('   - Authorized redirect URIs:');
  console.log('     \x1b[33mhttp://localhost:3000/auth/google/callback\x1b[0m');
  console.log('6. Copy Client ID and Secret');
  console.log('7. Edit the .env file and add them');
  console.log('\n' + '='.repeat(60));
  console.log('\nAfter getting credentials, edit the .env file in this folder.');
}

function askQuestion(question) {
  return new Promise((resolve) => {
    rl.question(question, resolve);
  });
}

setup();

.env.example

env

# ============================================
# GOOGLE OAUTH CONFIGURATION
# ============================================
# Get credentials from: https://console.cloud.google.com

# REQUIRED: Google OAuth 2.0 Credentials
GOOGLE_CLIENT_ID=your_client_id_here
GOOGLE_CLIENT_SECRET=your_client_secret_here

# OPTIONAL: Session Secret (auto-generated)
SESSION_SECRET=your_session_secret_here

# OPTIONAL: Server Port
PORT=3000

# ============================================
# NOTE: Copy this file to .env and fill values
# ============================================
.gitignore

# Dependencies
node_modules/
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# Environment
.env
.env.local

# Runtime
*.pid
*.seed
*.pid.lock

# Coverage
coverage/
.nyc_output/

# OS
.DS_Store
Thumbs.db

# IDE
.vscode/
.idea/
*.swp
*.swo

# Logs
logs/
*.log

public/index.html
html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Google OAuth Starter</title>
    <link rel="stylesheet" href="style.css">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
</head>
<body>
    <div class="container">
        <div class="card" id="loginCard">
            <div class="logo">
                <i class="fab fa-google"></i>
                <h1>Google OAuth Starter</h1>
            </div>
            
            <div class="content">
                <h2>Welcome to OAuth Demo</h2>
                <p>Sign in with Google to experience seamless authentication</p>
                
                <button id="googleLoginBtn" class="google-btn">
                    <i class="fab fa-google"></i>
                    Sign in with Google
                </button>
                
                <div class="instructions">
                    <h3><i class="fas fa-info-circle"></i> Quick Setup:</h3>
                    <ol>
                        <li>Get credentials from Google Cloud Console</li>
                        <li>Add to .env file in project root</li>
                        <li>Click button above to test</li>
                    </ol>
                </div>
            </div>
        </div>
        
        <div class="card" id="dashboardCard" style="display: none;">
            <div class="user-header">
                <img id="userAvatar" class="avatar" src="" alt="User Avatar">
                <div class="user-info">
                    <h2 id="userName">Welcome!</h2>
                    <p id="userEmail" class="email"></p>
                    <span class="badge">
                        <i class="fas fa-check-circle"></i>
                        Authenticated
                    </span>
                </div>
            </div>
            
            <div class="user-details">
                <h3><i class="fas fa-id-card"></i> Account Details</h3>
                <div class="details">
                    <div class="detail">
                        <span class="label">Status</span>
                        <span class="value active">Active</span>
                    </div>
                    <div class="detail">
                        <span class="label">Provider</span>
                        <span class="value" id="userProvider">Google</span>
                    </div>
                    <div class="detail">
                        <span class="label">Session</span>
                        <span class="value" id="sessionTime">Just now</span>
                    </div>
                </div>
            </div>
            
            <div class="actions">
                <button id="refreshBtn" class="btn secondary">
                    <i class="fas fa-sync-alt"></i> Refresh
                </button>
                <button id="logoutBtn" class="btn logout">
                    <i class="fas fa-sign-out-alt"></i> Sign Out
                </button>
            </div>
        </div>
        
        <footer class="footer">
            <p>Google OAuth Starter Kit • Ready for production</p>
            <div class="links">
                <a href="#" id="healthCheck"><i class="fas fa-heartbeat"></i> Health</a>
                <a href="#" id="setupHelp"><i class="fas fa-question-circle"></i> Help</a>
            </div>
        </footer>
    </div>
    
    <script src="app.js"></script>
</body>
</html>
public/style.css
css

* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    width: 100%;
    max-width: 500px;
}

.card {
    background: white;
    border-radius: 20px;
    padding: 40px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    animation: slideUp 0.5s ease;
}

@keyframes slideUp {
    from {
        opacity: 0;
        transform: translateY(20px);
    }
    to {
        opacity: 1;
        transform: translateY(0);
    }
}

.logo {
    text-align: center;
    margin-bottom: 30px;
}

.logo i {
    font-size: 50px;
    background: linear-gradient(135deg, #4285f4 0%, #34a853 100%);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    margin-bottom: 15px;
}

.logo h1 {
    font-size: 24px;
    color: #333;
    font-weight: 700;
}

.content {
    text-align: center;
}

.content h2 {
    color: #333;
    margin-bottom: 10px;
    font-size: 28px;
}

.content p {
    color: #666;
    margin-bottom: 30px;
    line-height: 1.6;
}

.google-btn {
    width: 100%;
    padding: 15px;
    background: #4285f4;
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 10px;
    transition: all 0.3s ease;
    margin-bottom: 30px;
}

.google-btn:hover {
    background: #3367d6;
    transform: translateY(-2px);
    box-shadow: 0 10px 25px rgba(66, 133, 244, 0.4);
}

.instructions {
    background: #f8f9fa;
    border-radius: 10px;
    padding: 20px;
    text-align: left;
}

.instructions h3 {
    color: #333;
    margin-bottom: 15px;
    font-size: 16px;
    display: flex;
    align-items: center;
    gap: 10px;
}

.instructions ol {
    padding-left: 20px;
    color: #555;
}

.instructions li {
    margin-bottom: 8px;
    line-height: 1.5;
}

.user-header {
    display: flex;
    align-items: center;
    gap: 20px;
    margin-bottom: 30px;
}

.avatar {
    width: 100px;
    height: 100px;
    border-radius: 50%;
    border: 4px solid #4285f4;
    object-fit: cover;
}

.user-info h2 {
    color: #333;
    font-size: 24px;
    margin-bottom: 5px;
}

.email {
    color: #666;
    margin-bottom: 10px;
}

.badge {
    display: inline-flex;
    align-items: center;
    gap: 8px;
    background: #d4edda;
    color: #155724;
    padding: 6px 12px;
    border-radius: 20px;
    font-size: 14px;
    font-weight: 600;
}

.badge i {
    color: #28a745;
}

.user-details {
    background: #f8f9fa;
    border-radius: 10px;
    padding: 20px;
    margin-bottom: 30px;
}

.user-details h3 {
    color: #333;
    margin-bottom: 15px;
    font-size: 18px;
    display: flex;
    align-items: center;
    gap: 10px;
}

.details {
    display: grid;
    gap: 15px;
}

.detail {
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.label {
    color: #666;
    font-weight: 600;
}

.value {
    color: #333;
    font-weight: 600;
}

.value.active {
    color: #28a745;
}

.actions {
    display: flex;
    gap: 15px;
}

.btn {
    flex: 1;
    padding: 12px;
    border: none;
    border-radius: 8px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    transition: all 0.3s ease;
}

.btn.secondary {
    background: #6c757d;
    color: white;
}

.btn.secondary:hover {
    background: #5a6268;
    transform: translateY(-2px);
}

.btn.logout {
    background: #dc3545;
    color: white;
}

.btn.logout:hover {
    background: #c82333;
    transform: translateY(-2px);
}

.footer {
    text-align: center;
    margin-top: 30px;
    color: white;
}

.footer p {
    margin-bottom: 15px;
    opacity: 0.9;
}

.links {
    display: flex;
    justify-content: center;
    gap: 20px;
}

.links a {
    color: white;
    text-decoration: none;
    display: flex;
    align-items: center;
    gap: 5px;
    opacity: 0.8;
    transition: opacity 0.3s ease;
}

.links a:hover {
    opacity: 1;
}

@media (max-width: 600px) {
    .card {
        padding: 25px;
    }
    
    .user-header {
        flex-direction: column;
        text-align: center;
    }
    
    .actions {
        flex-direction: column;
    }
}

public/app.js
javascript
document.addEventListener('DOMContentLoaded', function() {
    const loginCard = document.getElementById('loginCard');
    const dashboardCard = document.getElementById('dashboardCard');
    const googleLoginBtn = document.getElementById('googleLoginBtn');
    const logoutBtn = document.getElementById('logoutBtn');
    const refreshBtn = document.getElementById('refreshBtn');
    const healthCheck = document.getElementById('healthCheck');
    const setupHelp = document.getElementById('setupHelp');
    
    const userAvatar = document.getElementById('userAvatar');
    const userName = document.getElementById('userName');
    const userEmail = document.getElementById('userEmail');
    const userProvider = document.getElementById('userProvider');
    const sessionTime = document.getElementById('sessionTime');
    
    // Check authentication status
    checkAuth();
    
    // Event Listeners
    googleLoginBtn.addEventListener('click', () => {
        window.location.href = '/auth/google';
    });
    
    logoutBtn.addEventListener('click', () => {
        window.location.href = '/logout';
    });
    
    refreshBtn.addEventListener('click', checkAuth);
    
    healthCheck.addEventListener('click', async (e) => {
        e.preventDefault();
        try {
            const response = await fetch('/api/health');
            const data = await response.json();
            alert(`✅ Server Health:\nStatus: ${data.status}\nGoogle Configured: ${data.google_configured ? 'Yes' : 'No'}\nPort: ${data.port}`);
        } catch (error) {
            alert('❌ Server not responding');
        }
    });
    
    setupHelp.addEventListener('click', (e) => {
        e.preventDefault();
        alert('Need help?\n1. Get Google credentials from console.cloud.google.com\n2. Add them to .env file\n3. Restart the server');
    });
    
    // Functions
    async function checkAuth() {
        try {
            const response = await fetch('/api/user');
            const data = await response.json();
            
            if (data.authenticated) {
                showDashboard(data.user);
            } else {
                showLogin();
            }
        } catch (error) {
            console.error('Auth check failed:', error);
            showLogin();
        }
    }
    
    function showDashboard(user) {
        loginCard.style.display = 'none';
        dashboardCard.style.display = 'block';
        
        userName.textContent = user.name;
        userEmail.textContent = user.email;
        userProvider.textContent = user.provider || 'Google';
        sessionTime.textContent = new Date().toLocaleTimeString();
        
        if (user.photo && !user.photo.includes('default-avatar')) {
            userAvatar.src = user.photo;
        } else {
            // Generate avatar from initials
            const initials = user.name.split(' ').map(n => n[0]).join('').toUpperCase();
            userAvatar.src = `https://ui-avatars.com/api/?name=${encodeURIComponent(user.name)}&background=4285f4&color=fff`;
        }
    }
    
    function showLogin() {
        loginCard.style.display = 'block';
        dashboardCard.style.display = 'none';
    }
});
LICENSE (MIT License)
txt
MIT License

Copyright (c) 2024 Google OAuth Starter

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.



