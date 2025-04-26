# Learning pyTelegramBotAPI with One Project

We'll create a "Personal Task Manager Bot" that allows users to add, view, and complete tasks.

## Project: Personal Task Manager Bot

### 1. Setup and Basic Structure

First, install the library:
```bash
pip install pyTelegramBotAPI
```

Create `task_bot.py` with this basic structure:

```python
import telebot
from telebot import types
import json
import os

# Initialize bot with your token
bot = telebot.TeleBot('YOUR_TELEGRAM_BOT_TOKEN')

# Database file
DB_FILE = 'tasks.json'

# Load tasks from file
def load_tasks():
    if os.path.exists(DB_FILE):
        with open(DB_FILE, 'r') as f:
            return json.load(f)
    return {}

# Save tasks to file
def save_tasks(tasks):
    with open(DB_FILE, 'w') as f:
        json.dump(tasks, f)

# Start command
@bot.message_handler(commands=['start'])
def send_welcome(message):
    # Welcome message with keyboard
    markup = types.ReplyKeyboardMarkup(resize_keyboard=True)
    btn1 = types.KeyboardButton('➕ Add Task')
    btn2 = types.KeyboardButton('📋 My Tasks')
    markup.add(btn1, btn2)
    
    bot.send_message(
        message.chat.id,
        "Welcome to your Personal Task Manager!\n\n"
        "Use the buttons below to manage your tasks:",
        reply_markup=markup
    )

# Keep the bot running
bot.infinity_polling()
```

### 2. Adding Task Functionality

Add this code to handle adding new tasks:

```python
# Dictionary to store temporary task data
user_temp_data = {}

# Handle 'Add Task' button
@bot.message_handler(func=lambda message: message.text == '➕ Add Task')
def ask_for_task_title(message):
    msg = bot.send_message(message.chat.id, "What's the title of your task?")
    bot.register_next_step_handler(msg, process_task_title)

def process_task_title(message):
    chat_id = message.chat.id
    user_temp_data[chat_id] = {'title': message.text}
    
    msg = bot.send_message(chat_id, "Add a description (or type /skip):")
    bot.register_next_step_handler(msg, process_task_description)

def process_task_description(message):
    chat_id = message.chat.id
    if message.text == '/skip':
        description = "No description"
    else:
        description = message.text
    
    # Save the task
    tasks = load_tasks()
    if str(chat_id) not in tasks:
        tasks[str(chat_id)] = []
    
    tasks[str(chat_id)].append({
        'title': user_temp_data[chat_id]['title'],
        'description': description,
        'completed': False
    })
    
    save_tasks(tasks)
    del user_temp_data[chat_id]
    
    bot.send_message(chat_id, "✅ Task added successfully!")
```

### 3. Viewing Tasks Functionality

Add this code to handle viewing tasks:

```python
# Handle 'My Tasks' button
@bot.message_handler(func=lambda message: message.text == '📋 My Tasks')
def show_tasks(message):
    chat_id = message.chat.id
    tasks = load_tasks().get(str(chat_id), [])
    
    if not tasks:
        bot.send_message(chat_id, "You don't have any tasks yet!")
        return
    
    markup = types.InlineKeyboardMarkup()
    
    for index, task in enumerate(tasks):
        status = "✅" if task['completed'] else "❌"
        text = f"{index+1}. {task['title']} {status}"
        
        # Add buttons for each task
        btn_complete = types.InlineKeyboardButton(
            text='Complete' if not task['completed'] else 'Undo',
            callback_data=f'toggle_{index}'
        )
        btn_delete = types.InlineKeyboardButton(
            text='Delete',
            callback_data=f'delete_{index}'
        )
        markup.add(types.InlineKeyboardButton(text, callback_data=f'details_{index}'))
        markup.add(btn_complete, btn_delete)
    
    bot.send_message(chat_id, "Your Tasks:", reply_markup=markup)

# Handle callback queries (button presses)
@bot.callback_query_handler(func=lambda call: True)
def callback_query(call):
    chat_id = call.message.chat.id
    data = call.data
    tasks = load_tasks()
    user_tasks = tasks.get(str(chat_id), [])
    
    if data.startswith('details_'):
        index = int(data.split('_')[1])
        task = user_tasks[index]
        bot.answer_callback_query(
            call.id,
            f"Title: {task['title']}\n\nDescription: {task['description']}",
            show_alert=True
        )
    
    elif data.startswith('toggle_'):
        index = int(data.split('_')[1])
        user_tasks[index]['completed'] = not user_tasks[index]['completed']
        save_tasks(tasks)
        bot.edit_message_reply_markup(
            chat_id=chat_id,
            message_id=call.message.message_id,
            reply_markup=create_tasks_markup(user_tasks)
        )
    
    elif data.startswith('delete_'):
        index = int(data.split('_')[1])
        deleted_task = user_tasks.pop(index)
        save_tasks(tasks)
        bot.answer_callback_query(call.id, f"Deleted: {deleted_task['title']}")
        bot.edit_message_reply_markup(
            chat_id=chat_id,
            message_id=call.message.message_id,
            reply_markup=create_tasks_markup(user_tasks)
        )

def create_tasks_markup(tasks):
    markup = types.InlineKeyboardMarkup()
    for index, task in enumerate(tasks):
        status = "✅" if task['completed'] else "❌"
        text = f"{index+1}. {task['title']} {status}"
        
        btn_complete = types.InlineKeyboardButton(
            text='Complete' if not task['completed'] else 'Undo',
            callback_data=f'toggle_{index}'
        )
        btn_delete = types.InlineKeyboardButton(
            text='Delete',
            callback_data=f'delete_{index}'
        )
        markup.add(types.InlineKeyboardButton(text, callback_data=f'details_{index}'))
        markup.add(btn_complete, btn_delete)
    return markup
```

### 4. Additional Features

Let's add some extra commands:

```python
# Help command
@bot.message_handler(commands=['help'])
def send_help(message):
    help_text = """
📝 *Task Manager Bot Help* 📝

*/start* - Show welcome message
*/help* - Show this help message
*/add* - Add a new task
*/tasks* - Show your tasks
*/clear* - Delete all your tasks

You can also use the buttons that appear when you type /start.
"""
    bot.send_message(message.chat.id, help_text, parse_mode='Markdown')

# Clear all tasks
@bot.message_handler(commands=['clear'])
def clear_tasks(message):
    chat_id = message.chat.id
    tasks = load_tasks()
    
    if str(chat_id) in tasks and tasks[str(chat_id)]:
        markup = types.InlineKeyboardMarkup()
        btn_confirm = types.InlineKeyboardButton(
            text="Yes, delete all",
            callback_data='confirm_clear'
        )
        btn_cancel = types.InlineKeyboardButton(
            text="Cancel",
            callback_data='cancel_clear'
        )
        markup.add(btn_confirm, btn_cancel)
        
        bot.send_message(
            chat_id,
            "⚠️ Are you sure you want to delete ALL your tasks?",
            reply_markup=markup
        )
    else:
        bot.send_message(chat_id, "You don't have any tasks to delete.")

# Handle clear confirmation
@bot.callback_query_handler(func=lambda call: call.data in ['confirm_clear', 'cancel_clear'])
def handle_clear_confirmation(call):
    chat_id = call.message.chat.id
    tasks = load_tasks()
    
    if call.data == 'confirm_clear':
        tasks[str(chat_id)] = []
        save_tasks(tasks)
        bot.edit_message_text(
            "All tasks have been deleted.",
            chat_id=chat_id,
            message_id=call.message.message_id
        )
    else:
        bot.edit_message_text(
            "Clear operation cancelled.",
            chat_id=chat_id,
            message_id=call.message.message_id
        )
```

### 5. Running the Bot

To run your bot:
1. Replace `'YOUR_TELEGRAM_BOT_TOKEN'` with your actual bot token from BotFather
2. Run the script: `python task_bot.py`

## Key Concepts Learned

Through this project, you've learned:

1. **Bot Initialization**: Setting up a bot with your token
2. **Message Handlers**: Responding to commands and text messages
3. **Keyboards**: Creating both reply and inline keyboards
4. **Conversation Flow**: Using `register_next_step_handler` for multi-step interactions
5. **Callback Queries**: Handling button presses with inline keyboards
6. **Persistence**: Saving and loading data to/from a JSON file
7. **Basic Bot Structure**: Organizing a bot with multiple functions

## Next Steps

To enhance this bot and learn more, consider adding:
- Due dates for tasks with reminders
- Task categories or tags
- A better database system (like SQLite)
- User authentication if you want multiple users to share tasks
- Natural language processing for more intuitive task entry

This project gives you a solid foundation in pyTelegramBotAPI that you can build upon!
