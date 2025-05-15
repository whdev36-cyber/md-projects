# Telegram Voting Bot with pyTelegramBotAPI

Here's a complete project for a Telegram voting bot that collects users' phone numbers, locations, and full names.

## Project Structure

```
telegram_voting_bot/
│
├── config.py          # Configuration file (optional)
├── main.py            # Main bot file
├── database.py        # Database handling
└── requirements.txt   # Dependencies
```

## 1. Set up the environment

First, create a `requirements.txt` file:

```txt
pyTelegramBotAPI
python-dotenv
sqlalchemy
```

Install the dependencies:
```bash
pip install -r requirements.txt
```

## 2. Create config.py (optional)

```python
import os
from dotenv import load_dotenv

load_dotenv()

BOT_TOKEN = os.getenv('BOT_TOKEN')
ADMIN_ID = os.getenv('ADMIN_ID', '')
```

## 3. Create database.py

```python
from sqlalchemy import create_engine, Column, Integer, String, Text
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

Base = declarative_base()

class Vote(Base):
    __tablename__ = 'votes'
    
    id = Column(Integer, primary_key=True)
    user_id = Column(Integer)
    full_name = Column(String(100))
    phone_number = Column(String(20))
    latitude = Column(String(20))
    longitude = Column(String(20))
    address = Column(Text)

# SQLite database
engine = create_engine('sqlite:///votes.db')
Base.metadata.create_all(engine)

Session = sessionmaker(bind=engine)

def add_vote(user_id, full_name, phone_number, latitude, longitude, address):
    session = Session()
    vote = Vote(
        user_id=user_id,
        full_name=full_name,
        phone_number=phone_number,
        latitude=latitude,
        longitude=longitude,
        address=address
    )
    session.add(vote)
    session.commit()
    session.close()

def get_all_votes():
    session = Session()
    votes = session.query(Vote).all()
    session.close()
    return votes
```

## 4. Create main.py

```python
import telebot
from telebot import types
from database import add_vote, get_all_votes
import config

bot = telebot.TeleBot(config.BOT_TOKEN)

# User states
USER_STATES = {}

class UserState:
    def __init__(self):
        self.waiting_for_name = False
        self.waiting_for_phone = False
        self.waiting_for_location = False
        self.full_name = None
        self.phone_number = None

@bot.message_handler(commands=['start'])
def send_welcome(message):
    user_id = message.from_user.id
    USER_STATES[user_id] = UserState()
    
    markup = types.ReplyKeyboardMarkup(resize_keyboard=True)
    item = types.KeyboardButton("Start Voting")
    markup.add(item)
    
    bot.send_message(
        message.chat.id,
        "Welcome to the Voting System!\n\n"
        "Click 'Start Voting' to begin the process.",
        reply_markup=markup
    )

@bot.message_handler(func=lambda message: message.text == "Start Voting")
def start_voting(message):
    user_id = message.from_user.id
    if user_id not in USER_STATES:
        USER_STATES[user_id] = UserState()
    
    USER_STATES[user_id].waiting_for_name = True
    
    bot.send_message(
        message.chat.id,
        "Please enter your full name:",
        reply_markup=types.ReplyKeyboardRemove()
    )

@bot.message_handler(func=lambda message: USER_STATES.get(message.from_user.id, UserState()).waiting_for_name)
def get_full_name(message):
    user_id = message.from_user.id
    USER_STATES[user_id].full_name = message.text
    USER_STATES[user_id].waiting_for_name = False
    USER_STATES[user_id].waiting_for_phone = True
    
    markup = types.ReplyKeyboardMarkup(resize_keyboard=True)
    item = types.KeyboardButton("Share Phone Number", request_contact=True)
    markup.add(item)
    
    bot.send_message(
        message.chat.id,
        "Now please share your phone number:",
        reply_markup=markup
    )

@bot.message_handler(content_types=['contact'], 
                    func=lambda message: USER_STATES.get(message.from_user.id, UserState()).waiting_for_phone)
def get_phone_number(message):
    user_id = message.from_user.id
    if message.contact is not None:
        USER_STATES[user_id].phone_number = message.contact.phone_number
        USER_STATES[user_id].waiting_for_phone = False
        USER_STATES[user_id].waiting_for_location = True
        
        markup = types.ReplyKeyboardMarkup(resize_keyboard=True)
        item = types.KeyboardButton("Share Location", request_location=True)
        markup.add(item)
        
        bot.send_message(
            message.chat.id,
            "Finally, please share your location:",
            reply_markup=markup
        )

@bot.message_handler(content_types=['location'], 
                    func=lambda message: USER_STATES.get(message.from_user.id, UserState()).waiting_for_location)
def get_location(message):
    user_id = message.from_user.id
    if message.location is not None:
        # Get address (optional - you might need a geocoding API for this)
        address = "Location received (address not resolved)"
        
        # Save to database
        add_vote(
            user_id=user_id,
            full_name=USER_STATES[user_id].full_name,
            phone_number=USER_STATES[user_id].phone_number,
            latitude=message.location.latitude,
            longitude=message.location.longitude,
            address=address
        )
        
        # Reset state
        del USER_STATES[user_id]
        
        bot.send_message(
            message.chat.id,
            "Thank you for voting! Your information has been recorded.",
            reply_markup=types.ReplyKeyboardRemove()
        )

@bot.message_handler(commands=['results'])
def show_results(message):
    if str(message.from_user.id) != config.ADMIN_ID:
        bot.reply_to(message, "You are not authorized to view results.")
        return
    
    votes = get_all_votes()
    if not votes:
        bot.send_message(message.chat.id, "No votes recorded yet.")
        return
    
    response = "Voting Results:\n\n"
    for vote in votes:
        response += (
            f"User ID: {vote.user_id}\n"
            f"Name: {vote.full_name}\n"
            f"Phone: {vote.phone_number}\n"
            f"Location: {vote.latitude}, {vote.longitude}\n"
            f"Address: {vote.address}\n"
            "------------------\n"
        )
    
    bot.send_message(message.chat.id, response)

if __name__ == '__main__':
    print("Bot is running...")
    bot.infinity_polling()
```

## How to Use the Bot

1. Users start with `/start` command
2. They click "Start Voting" button
3. The bot asks for:
   - Full name (text input)
   - Phone number (shared via contact)
   - Location (shared via location)
4. After all information is collected, it's saved to the database
5. Admins can view all votes with `/results` command

## Important Notes

1. **Privacy Considerations**:
   - Inform users how their data will be used
   - Comply with data protection regulations in your region

2. **Security**:
   - Restrict `/results` to admin only
   - Consider adding more security measures for production

3. **Improvements**:
   - Add data validation
   - Implement a proper geocoding service to get addresses from coordinates
   - Add error handling
   - Implement a proper user state management (this example uses a simple dictionary)

4. **Deployment**:
   - You can deploy this on any server that can run Python
   - For production, consider using PostgreSQL instead of SQLite

To run the bot:
```bash
python main.py
```

Remember to create a `.env` file with your bot token:
```
BOT_TOKEN=your_bot_token_here
ADMIN_ID=your_admin_user_id
```