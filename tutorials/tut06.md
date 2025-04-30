# Python Telethon Mastery Guide

This comprehensive guide covers the essential concepts for mastering the Telethon library for Telegram API interaction in Python.

## 📚 1. Core Concepts

### `TelegramClient`
The main class that connects to Telegram's servers:
```python
from telethon import TelegramClient

client = TelegramClient('session_name', api_id, api_hash)
```

### `events`
Event handlers for responding to Telegram activities:
```python
@client.on(events.NewMessage(pattern='hello'))
async def handler(event):
    await event.reply('Hi there!')
```

### Functions & Types
- `functions`: API methods you can call (e.g., sending messages)
- `types`: Objects returned by Telegram (users, messages, etc.)

### Sessions
Persistent connection state storage:
```python
with TelegramClient(StringSession(), api_id, api_hash) as client:
    print(client.session.save())  # String session
```

## 💬 2. Sending & Receiving Messages

### Basic Messaging
```python
# Send message
await client.send_message('username', 'Hello!')

# Send file
await client.send_file('username', '/path/to/file.jpg')

# Download media
await client.download_media(message, 'downloads/')

# Iterate messages
async for message in client.iter_messages('username', limit=10):
    print(message.text)

# Delete messages
await client.delete_messages(chat, [message_ids])
```

## 👤 3. Users, Contacts, Chats

### User and Chat Management
```python
# Get your own info
me = await client.get_me()

# Get entity by username/phone/ID
entity = await client.get_entity('username')

# Get chat participants
participants = await client.get_participants('group_username')

# Add contact
from telethon.tl.functions.contacts import AddContactRequest
await client(AddContactRequest(
    id='user_id',
    first_name='John',
    last_name='Doe',
    phone='+123456789'
))
```

## 📡 4. Events Handling

### Common Event Handlers
```python
# New message handler
@client.on(events.NewMessage(incoming=True))
async def new_message_handler(event):
    print(f"New message: {event.message.text}")

# Edited message handler
@client.on(events.MessageEdited)
async def edited_handler(event):
    print(f"Message edited: {event.message.text}")

# Callback query (inline button press)
@client.on(events.CallbackQuery)
async def callback_handler(event):
    await event.answer("Button clicked!")
```

## 🔐 5. Authentication & Sessions

### Authentication Flow
```python
# Start client
async with client:
    # For first run (will prompt for phone number)
    await client.start()
    
    # Or sign in programmatically
    await client.sign_in(phone='+123456789')
    await client.sign_in(code='12345')  # Sent via Telegram
    
    # For bots
    await client.start(bot_token='123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11')
    
    # Save session string
    print("Session string:", client.session.save())
```

## 🔁 6. Iterators

### Efficient Data Retrieval
```python
# Iterate through dialogs (chats)
async for dialog in client.iter_dialogs():
    print(dialog.name, dialog.id)

# Iterate messages with filters
async for message in client.iter_messages('group', search='query', limit=100):
    print(message.sender_id, message.text)

# Iterate admin log events
async for event in client.iter_admin_log('group'):
    print(event.user_id, event.action)
```

## 🧩 7. Telegram API Functions

### Common API Functions
```python
from telethon.tl.functions.messages import SendMessageRequest
from telethon.tl.functions.users import GetFullUserRequest

# Raw API call example
result = await client(SendMessageRequest(
    peer='username',
    message='Hello from raw API!',
    no_webpage=True
))

# Get full user info
full_user = await client(GetFullUserRequest('username'))
```

## � 8. Telegram API Types

### Common Types
```python
from telethon.tl.types import InputPeerUser, ReplyKeyboardMarkup

# Creating input peer
peer = InputPeerUser(user_id=12345, access_hash='abcdef123')

# Creating reply keyboard
keyboard = ReplyKeyboardMarkup(
    [[KeyboardButton(text="Option 1"), KeyboardButton(text="Option 2")]],
    resize=True
)
```

## 🔧 9. Utility Features

### Helpful Utilities
```python
from telethon import utils, Button

# Get input entity from various formats
entity = utils.get_input_entity('username')

# Inline buttons
buttons = [
    [Button.inline("Yes", b'yes'), Button.inline("No", b'no')],
    [Button.url("Visit", "https://example.com")]
]
await client.send_message('username', 'Choose:', buttons=buttons)
```

## 🧪 10. Debugging & Logging

### Debugging Techniques
```python
import logging

# Enable debug logging
logging.basicConfig(level=logging.DEBUG)

# You can also enable Telethon's debug mode
client = TelegramClient('session', api_id, api_hash, base_logger=logging.getLogger())

# To trace raw API calls
client.set_parse_mode(None)  # Disables automatic parsing
```

This guide covers the fundamental aspects of Telethon. For more advanced usage, refer to the [official Telethon documentation](https://docs.telethon.dev).