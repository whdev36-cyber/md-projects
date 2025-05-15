# Telegram Web App Bot with User Registration

Here's a complete implementation of a Telegram bot with Web App functionality that registers users, using SQLAlchemy for database operations and serving a local HTML file through Telegram's Web App CDN.

## Project Structure

```
telegram-webapp-bot/
├── app.py                # Main bot application
├── config.py             # Configuration file
├── models.py             # SQLAlchemy models
├── static/
│   └── index.html        # Web App HTML file
├── requirements.txt      # Dependencies
└── database.db           # SQLite database (will be created)
```

## 1. Set up the project

First, create a `requirements.txt` file:

```txt
python-telegram-bot==20.3
sqlalchemy==2.0.23
python-dotenv==1.0.0
```

Install dependencies:
```bash
pip install -r requirements.txt
```

## 2. Configuration (config.py)

```python
import os
from dotenv import load_dotenv

load_dotenv()

class Config:
    TOKEN = os.getenv('TELEGRAM_BOT_TOKEN')
    WEBHOOK_URL = os.getenv('WEBHOOK_URL', '')
    WEBAPP_HOST = '0.0.0.0'
    WEBAPP_PORT = int(os.getenv('PORT', 5000))
    DATABASE_URL = os.getenv('DATABASE_URL', 'sqlite:///database.db')
```

Create a `.env` file:
```
TELEGRAM_BOT_TOKEN=your_bot_token_here
```

## 3. Database Models (models.py)

```python
from sqlalchemy import create_engine, Column, Integer, String, Boolean, DateTime
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker
from datetime import datetime
from config import Config

Base = declarative_base()

class User(Base):
    __tablename__ = 'users'
    
    id = Column(Integer, primary_key=True)
    telegram_id = Column(Integer, unique=True, nullable=False)
    first_name = Column(String(100))
    last_name = Column(String(100))
    username = Column(String(100))
    is_active = Column(Boolean, default=True)
    registered_at = Column(DateTime, default=datetime.utcnow)
    last_seen = Column(DateTime, default=datetime.utcnow)

    def __repr__(self):
        return f"<User {self.username} ({self.first_name} {self.last_name})>"

# Database setup
engine = create_engine(Config.DATABASE_URL)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

def init_db():
    Base.metadata.create_all(bind=engine)

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

## 4. Web App HTML (static/index.html)

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Telegram Web App</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <style>
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f5f5f5;
            color: #333;
        }
        .container {
            max-width: 600px;
            margin: 0 auto;
            padding: 20px;
        }
        .header {
            text-align: center;
            margin-bottom: 20px;
        }
        .form-group {
            margin-bottom: 15px;
        }
        button {
            background-color: #0088cc;
            color: white;
            border: none;
            padding: 10px 15px;
            border-radius: 5px;
            cursor: pointer;
            width: 100%;
            font-size: 16px;
        }
        button:hover {
            background-color: #0077bb;
        }
        #userData {
            margin-top: 20px;
            padding: 15px;
            background-color: white;
            border-radius: 5px;
            box-shadow: 0 1px 3px rgba(0,0,0,0.1);
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>Welcome to our Web App!</h1>
            <p>Register with your Telegram account</p>
        </div>
        
        <div id="userData">
            <p>Loading your Telegram data...</p>
        </div>
        
        <button id="registerBtn">Register Now</button>
    </div>

    <script>
        // Initialize Telegram Web App
        const tg = window.Telegram.WebApp;
        
        // Expand the Web App to full viewport
        tg.expand();
        
        // Get user data from Telegram
        const user = tg.initDataUnsafe.user;
        
        // Display user data
        const userDataDiv = document.getElementById('userData');
        
        if (user) {
            userDataDiv.innerHTML = `
                <p><strong>First Name:</strong> ${user.first_name}</p>
                ${user.last_name ? `<p><strong>Last Name:</strong> ${user.last_name}</p>` : ''}
                ${user.username ? `<p><strong>Username:</strong> @${user.username}</p>` : ''}
            `;
        } else {
            userDataDiv.innerHTML = '<p>Could not load Telegram user data.</p>';
        }
        
        // Handle registration button click
        document.getElementById('registerBtn').addEventListener('click', () => {
            tg.showPopup({
                title: 'Confirm Registration',
                message: 'Do you want to register with your Telegram account?',
                buttons: [
                    {id: 'confirm', type: 'ok'},
                    {id: 'cancel', type: 'cancel'}
                ]
            }, (buttonId) => {
                if (buttonId === 'confirm') {
                    // Send data to bot
                    tg.sendData(JSON.stringify({
                        action: 'register',
                        user: user
                    }));
                    
                    // Close the Web App
                    tg.close();
                }
            });
        });
        
        // Change button color to match Telegram theme
        tg.onEvent('themeChanged', () => {
            document.body.style.backgroundColor = tg.themeParams.bg_color || '#f5f5f5';
        });
        
        // Set initial theme
        document.body.style.backgroundColor = tg.themeParams.bg_color || '#f5f5f5';
    </script>
</body>
</html>
```

## 5. Main Application (app.py)

```python
from telegram import Update, WebAppInfo
from telegram.ext import Application, CommandHandler, ContextTypes, MessageHandler, filters, CallbackQueryHandler
from telegram.constants import ParseMode
import sqlalchemy
from models import User, get_db, init_db
from config import Config
import os
from datetime import datetime

# Initialize database
init_db()

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Send a message with a button that opens the web app."""
    web_app_url = f"https://{Config.WEBHOOK_URL}/static/index.html" if Config.WEBHOOK_URL else "https://your-webapp-url.com/static/index.html"
    
    await update.message.reply_text(
        "Welcome! Please register using our web app:",
        reply_markup={
            "inline_keyboard": [[
                {
                    "text": "Open Web App",
                    "web_app": {"url": web_app_url}
                }
            ]]
        }
    )

async def handle_web_app_data(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Handle data received from the web app."""
    data = json.loads(update.effective_message.web_app_data.data)
    user_data = data.get('user')
    
    if data.get('action') == 'register':
        db = next(get_db())
        
        try:
            # Check if user already exists
            existing_user = db.query(User).filter(User.telegram_id == user_data['id']).first()
            
            if existing_user:
                await update.effective_message.reply_text(
                    "You are already registered!",
                    parse_mode=ParseMode.HTML
                )
            else:
                # Create new user
                new_user = User(
                    telegram_id=user_data['id'],
                    first_name=user_data.get('first_name'),
                    last_name=user_data.get('last_name'),
                    username=user_data.get('username')
                )
                db.add(new_user)
                db.commit()
                
                await update.effective_message.reply_text(
                    "🎉 Registration successful! Welcome aboard!",
                    parse_mode=ParseMode.HTML
                )
        except Exception as e:
            db.rollback()
            await update.effective_message.reply_text(
                "⚠️ An error occurred during registration. Please try again.",
                parse_mode=ParseMode.HTML
            )
        finally:
            db.close()

async def serve_static_file(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Serve static files for the web app."""
    # This is a placeholder - in a real implementation, you'd use a proper web server
    # like Flask or FastAPI to serve static files
    pass

def main():
    """Start the bot."""
    application = Application.builder().token(Config.TOKEN).build()
    
    # Command handlers
    application.add_handler(CommandHandler("start", start))
    
    # Web app data handler
    application.add_handler(MessageHandler(filters.StatusUpdate.WEB_APP_DATA, handle_web_app_data))
    
    # Run the bot
    if Config.WEBHOOK_URL:
        application.run_webhook(
            listen=Config.WEBAPP_HOST,
            port=Config.WEBAPP_PORT,
            url_path=Config.TOKEN,
            webhook_url=f"{Config.WEBHOOK_URL}/{Config.TOKEN}"
        )
    else:
        application.run_polling()

if __name__ == "__main__":
    main()
```

## Deployment Notes

1. **For local testing**:
   - Run the bot with `python app.py`
   - Use ngrok to expose your local server: `ngrok http 5000`
   - Update your `.env` with the ngrok URL:
     ```
     WEBHOOK_URL=your-ngrok-url.ngrok.io
     ```

2. **For production**:
   - Deploy to a hosting provider like Heroku, Render, or AWS
   - Set up proper static file serving (Flask/FastAPI)
   - Configure the webhook URL in your `.env`

3. **Bot Setup**:
   - Create a bot via BotFather
   - Set up the web app in BotFather with the command:
     ```
     /setmenubutton
     ```
     Then select your bot and provide the web app URL

## Key Features

1. **User Registration**: Stores user data from Telegram in SQLite database
2. **Web App Integration**: Uses Telegram's Web App CDN for the frontend
3. **Interactive UI**: Confirmation popup and theme-aware design
4. **Database Operations**: SQLAlchemy for ORM and database management

Remember to handle sensitive data properly in production and implement additional security measures like input validation and error handling.