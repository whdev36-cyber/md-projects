# Learning Telethon by Building a BIG Userbot

Telethon is a powerful Python 3 MTProto library for interacting with Telegram's API. Building a userbot is a great way to learn Telethon's capabilities. Here's a comprehensive guide to get you started:

## Prerequisites

1. Python 3.6 or higher installed
2. A Telegram API ID and API Hash (get from [my.telegram.org](https://my.telegram.org))
3. Basic Python knowledge

## Step 1: Setup Your Project

```bash
# Create project directory
mkdir big-userbot
cd big-userbot

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install Telethon
pip install telethon
```

## Step 2: Basic Client Setup

Create `main.py` with this basic structure:

```python
from telethon import TelegramClient, events
import config

client = TelegramClient('big_userbot', config.API_ID, config.API_HASH)

async def main():
    # You can print all the dialogs/conversations that your account is part of:
    async for dialog in client.iter_dialogs():
        print(dialog.name, 'has ID', dialog.id)

with client:
    client.loop.run_until_complete(main())
```

Create `config.py` to store your credentials:

```python
API_ID = 12345  # Your API ID
API_HASH = "your_api_hash_here"  # Your API Hash
```

## Step 3: Adding Core Functionality

### Message Handling

```python
@client.on(events.NewMessage(pattern='(?i)hello'))
async def handler(event):
    await event.reply('Hi there!')
```

### File Uploading

```python
@client.on(events.NewMessage(pattern='/upload'))
async def upload_handler(event):
    if event.is_reply:
        reply = await event.get_reply_message()
        if reply.media:
            file = await client.download_media(reply)
            await client.send_file(event.chat_id, file, caption="Here's your file!")
```

### User Information

```python
@client.on(events.NewMessage(pattern='/info'))
async def info_handler(event):
    if event.is_reply:
        user = await event.get_reply_message()
        await event.reply(f"User ID: {user.sender_id}\nFirst Name: {user.sender.first_name}")
```

## Step 4: Advanced Features

### Database Integration

```python
# Example with SQLite
import sqlite3

def init_db():
    conn = sqlite3.connect('userbot.db')
    c = conn.cursor()
    c.execute('''CREATE TABLE IF NOT EXISTS notes
                 (user_id integer, note_name text, note_content text)''')
    conn.commit()
    conn.close()

@client.on(events.NewMessage(pattern='/save (.+)'))
async def save_note(event):
    note_name = event.pattern_match.group(1)
    if event.is_reply:
        reply = await event.get_reply_message()
        # Save to database
        conn = sqlite3.connect('userbot.db')
        c = conn.cursor()
        c.execute("INSERT INTO notes VALUES (?, ?, ?)", 
                 (event.sender_id, note_name, reply.text))
        conn.commit()
        conn.close()
        await event.reply(f"Note '{note_name}' saved!")
```

### Plugin System

```python
# plugins/example.py
def register(client):
    @client.on(events.NewMessage(pattern='/example'))
    async def example_handler(event):
        await event.reply("This is an example plugin!")

# In main.py
import importlib
import os

def load_plugins():
    for filename in os.listdir('plugins'):
        if filename.endswith('.py') and not filename.startswith('_'):
            plugin = importlib.import_module(f'plugins.{filename[:-3]}')
            plugin.register(client)
```

## Step 5: Running Your Userbot

```python
if __name__ == '__main__':
    init_db()
    load_plugins()
    print("BIG Userbot is running...")
    client.start()
    client.run_until_disconnected()
```

## Best Practices

1. **Error Handling**: Always wrap your handlers in try-except blocks
2. **Rate Limiting**: Be mindful of Telegram's API limits
3. **Privacy**: Never log or store sensitive user data without permission
4. **Configuration**: Use environment variables for sensitive data
5. **Logging**: Implement proper logging for debugging

## Next Steps

1. Add more plugins (weather, translation, etc.)
2. Implement a web dashboard for control
3. Add support for multiple accounts
4. Create a deployment system (Docker, etc.)
5. Implement automated backups

Remember that userbots should comply with Telegram's Terms of Service. Use responsibly!
