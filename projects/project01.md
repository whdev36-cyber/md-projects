# Creating a Flexible GUI with Kivy (Screen Manager)

## Complete Code (main.py)

```python
from kivy.app import App
from kivy.uix.screenmanager import ScreenManager, Screen
from kivy.lang import Builder
from kivy.properties import StringProperty
import os
import shutil

# Define the KV language string for our UI
KV = '''
#:import FadeTransition kivy.uix.screenmanager.FadeTransition

<HomeScreen>:
    BoxLayout:
        orientation: 'vertical'
        padding: 20
        spacing: 20
        
        Label:
            text: 'PC Cleaner Pro'
            font_size: '32sp'
            bold: True
            size_hint_y: None
            height: 50
            
        Image:
            source: 'assets/cleaner.png'  # Replace with your image
            size_hint_y: None
            height: 200
            
        GridLayout:
            cols: 2
            spacing: 10
            size_hint_y: None
            height: 120
            
            Button:
                text: 'Register'
                on_press: root.manager.current = 'register'
                background_color: 0.2, 0.6, 1, 1
                
            Button:
                text: 'Login'
                on_press: root.manager.current = 'login'
                background_color: 0.2, 0.8, 0.4, 1
                
            Button:
                text: 'Clean Now'
                on_press: root.manager.current = 'cleaning'
                background_color: 1, 0.5, 0, 1
                
            Button:
                text: 'Settings'
                on_press: root.manager.current = 'settings'
                background_color: 0.8, 0.2, 0.8, 1
                
        Button:
            text: 'About'
            on_press: root.manager.current = 'about'
            size_hint_y: None
            height: 50
            background_color: 0.5, 0.5, 0.5, 1

<RegisterScreen>:
    BoxLayout:
        orientation: 'vertical'
        padding: 30
        spacing: 15
        
        Label:
            text: 'Create Account'
            font_size: '24sp'
            bold: True
            size_hint_y: None
            height: 40
            
        TextInput:
            id: username
            hint_text: 'Username'
            size_hint_y: None
            height: 50
            
        TextInput:
            id: email
            hint_text: 'Email'
            size_hint_y: None
            height: 50
            
        TextInput:
            id: password
            hint_text: 'Password'
            password: True
            size_hint_y: None
            height: 50
            
        TextInput:
            id: confirm_password
            hint_text: 'Confirm Password'
            password: True
            size_hint_y: None
            height: 50
            
        Button:
            text: 'Register'
            size_hint_y: None
            height: 50
            on_press: root.register()
            background_color: 0.2, 0.6, 1, 1
            
        Button:
            text: 'Back to Home'
            size_hint_y: None
            height: 40
            on_press: root.manager.current = 'home'
            background_color: 0.8, 0.2, 0.2, 1

<LoginScreen>:
    BoxLayout:
        orientation: 'vertical'
        padding: 30
        spacing: 15
        
        Label:
            text: 'Login'
            font_size: '24sp'
            bold: True
            size_hint_y: None
            height: 40
            
        TextInput:
            id: username
            hint_text: 'Username'
            size_hint_y: None
            height: 50
            
        TextInput:
            id: password
            hint_text: 'Password'
            password: True
            size_hint_y: None
            height: 50
            
        Button:
            text: 'Login'
            size_hint_y: None
            height: 50
            on_press: root.login()
            background_color: 0.2, 0.8, 0.4, 1
            
        Button:
            text: 'Back to Home'
            size_hint_y: None
            height: 40
            on_press: root.manager.current = 'home'
            background_color: 0.8, 0.2, 0.2, 1

<CleaningScreen>:
    BoxLayout:
        orientation: 'vertical'
        padding: 20
        spacing: 15
        
        Label:
            text: 'Clean Your PC'
            font_size: '24sp'
            bold: True
            size_hint_y: None
            height: 40
            
        BoxLayout:
            orientation: 'vertical'
            spacing: 10
            
            CheckBox:
                id: temp_files
                group: 'clean_options'
                active: True
                size_hint_y: None
                height: 40
                
            Label:
                text: 'Temporary Files'
                size_hint_y: None
                height: 40
                
            CheckBox:
                id: cache
                group: 'clean_options'
                active: True
                size_hint_y: None
                height: 40
                
            Label:
                text: 'Browser Cache'
                size_hint_y: None
                height: 40
                
            CheckBox:
                id: logs
                group: 'clean_options'
                active: False
                size_hint_y: None
                height: 40
                
            Label:
                text: 'System Logs'
                size_hint_y: None
                height: 40
                
            CheckBox:
                id: recycle_bin
                group: 'clean_options'
                active: False
                size_hint_y: None
                height: 40
                
            Label:
                text: 'Empty Recycle Bin'
                size_hint_y: None
                height: 40
                
        Button:
            text: 'Start Cleaning'
            size_hint_y: None
            height: 50
            on_press: root.start_cleaning()
            background_color: 1, 0.5, 0, 1
            
        Button:
            text: 'Back to Home'
            size_hint_y: None
            height: 40
            on_press: root.manager.current = 'home'
            background_color: 0.8, 0.2, 0.2, 1

<ResultScreen>:
    BoxLayout:
        orientation: 'vertical'
        padding: 20
        spacing: 15
        
        Label:
            text: 'Cleaning Results'
            font_size: '24sp'
            bold: True
            size_hint_y: None
            height: 40
            
        ScrollView:
            Label:
                id: results
                text: root.results_text
                text_size: self.width, None
                size_hint_y: None
                height: self.texture_size[1]
                halign: 'left'
                valign: 'top'
                
        Button:
            text: 'Back to Home'
            size_hint_y: None
            height: 50
            on_press: root.manager.current = 'home'
            background_color: 0.2, 0.6, 1, 1

<SettingsScreen>:
    BoxLayout:
        orientation: 'vertical'
        padding: 20
        spacing: 15
        
        Label:
            text: 'Settings'
            font_size: '24sp'
            bold: True
            size_hint_y: None
            height: 40
            
        BoxLayout:
            orientation: 'vertical'
            spacing: 10
            
            Label:
                text: 'Theme:'
                size_hint_y: None
                height: 30
                
            Spinner:
                id: theme
                text: 'Light'
                values: ['Light', 'Dark', 'Blue']
                size_hint_y: None
                height: 40
                
            Label:
                text: 'Auto-clean on startup:'
                size_hint_y: None
                height: 30
                
            CheckBox:
                id: auto_clean
                size_hint_y: None
                height: 40
                
        Button:
            text: 'Save Settings'
            size_hint_y: None
            height: 50
            on_press: root.save_settings()
            background_color: 0.8, 0.2, 0.8, 1
            
        Button:
            text: 'Back to Home'
            size_hint_y: None
            height: 40
            on_press: root.manager.current = 'home'
            background_color: 0.8, 0.2, 0.2, 1

<AboutScreen>:
    BoxLayout:
        orientation: 'vertical'
        padding: 20
        spacing: 15
        
        Label:
            text: 'About PC Cleaner Pro'
            font_size: '24sp'
            bold: True
            size_hint_y: None
            height: 40
            
        ScrollView:
            Label:
                text: 'PC Cleaner Pro\\n\\nVersion: 1.0.0\\n\\nDeveloped by: Your Company\\n\\nDescription:\\nThis application helps you clean junk files from your computer to improve performance and free up disk space.\\n\\nFeatures:\\n- Clean temporary files\\n- Clear browser cache\\n- Remove system logs\\n- Empty recycle bin\\n\\nContact: support@yourcompany.com'
                text_size: self.width, None
                size_hint_y: None
                height: self.texture_size[1]
                halign: 'left'
                valign: 'top'
                
        Button:
            text: 'Back to Home'
            size_hint_y: None
            height: 50
            on_press: root.manager.current = 'home'
            background_color: 0.5, 0.5, 0.5, 1
'''

# Define screen classes
class HomeScreen(Screen):
    pass

class RegisterScreen(Screen):
    def register(self):
        username = self.ids.username.text
        email = self.ids.email.text
        password = self.ids.password.text
        confirm_password = self.ids.confirm_password.text
        
        if not all([username, email, password, confirm_password]):
            self.show_error("All fields are required!")
            return
            
        if password != confirm_password:
            self.show_error("Passwords don't match!")
            return
            
        # Here you would typically save to database
        print(f"Registered user: {username}, {email}")
        self.manager.current = 'home'
        
    def show_error(self, message):
        # In a real app, you'd show this in the UI
        print(f"Error: {message}")

class LoginScreen(Screen):
    def login(self):
        username = self.ids.username.text
        password = self.ids.password.text
        
        if not all([username, password]):
            self.show_error("Both fields are required!")
            return
            
        # Here you would typically validate credentials
        print(f"Login attempt for user: {username}")
        self.manager.current = 'home'
        
    def show_error(self, message):
        # In a real app, you'd show this in the UI
        print(f"Error: {message}")

class CleaningScreen(Screen):
    def start_cleaning(self):
        # Get which options are selected
        clean_temp = self.ids.temp_files.active
        clean_cache = self.ids.cache.active
        clean_logs = self.ids.logs.active
        empty_recycle = self.ids.recycle_bin.active
        
        results = []
        
        # Perform cleaning based on selections
        if clean_temp:
            result = self.clean_temp_files()
            results.append(f"Temporary Files: {result}")
            
        if clean_cache:
            result = self.clean_browser_cache()
            results.append(f"Browser Cache: {result}")
            
        if clean_logs:
            result = self.clean_system_logs()
            results.append(f"System Logs: {result}")
            
        if empty_recycle:
            result = self.empty_recycle_bin()
            results.append(f"Recycle Bin: {result}")
            
        # Pass results to results screen
        result_screen = self.manager.get_screen('results')
        result_screen.results_text = "\\n".join(results)
        self.manager.current = 'results'
    
    def clean_temp_files(self):
        try:
            temp_dir = os.path.join(os.environ['USERPROFILE'], 'AppData', 'Local', 'Temp')
            for root, dirs, files in os.walk(temp_dir):
                for file in files:
                    try:
                        os.unlink(os.path.join(root, file))
                    except:
                        continue
            return "Cleaned successfully"
        except Exception as e:
            return f"Error: {str(e)}"
    
    def clean_browser_cache(self):
        try:
            # Chrome cache
            chrome_cache = os.path.join(os.environ['USERPROFILE'], 'AppData', 'Local', 
                                      'Google', 'Chrome', 'User Data', 'Default', 'Cache')
            if os.path.exists(chrome_cache):
                shutil.rmtree(chrome_cache, ignore_errors=True)
            
            # Edge cache
            edge_cache = os.path.join(os.environ['USERPROFILE'], 'AppData', 'Local', 
                                     'Microsoft', 'Edge', 'User Data', 'Default', 'Cache')
            if os.path.exists(edge_cache):
                shutil.rmtree(edge_cache, ignore_errors=True)
                
            return "Cleaned successfully"
        except Exception as e:
            return f"Error: {str(e)}"
    
    def clean_system_logs(self):
        try:
            logs_dir = os.path.join(os.environ['SystemRoot'], 'Logs')
            for root, dirs, files in os.walk(logs_dir):
                for file in files:
                    if file.endswith('.log'):
                        try:
                            os.unlink(os.path.join(root, file))
                        except:
                            continue
            return "Cleaned successfully"
        except Exception as e:
            return f"Error: {str(e)}"
    
    def empty_recycle_bin(self):
        try:
            from winshell import recycle_bin
            recycle_bin().empty(confirm=False, show_progress=False, sound=False)
            return "Emptied successfully"
        except ImportError:
            return "winshell module not installed"
        except Exception as e:
            return f"Error: {str(e)}"

class ResultScreen(Screen):
    results_text = StringProperty("Cleaning results will appear here...")

class SettingsScreen(Screen):
    def save_settings(self):
        theme = self.ids.theme.text
        auto_clean = self.ids.auto_clean.active
        
        # Here you would typically save these settings
        print(f"Settings saved - Theme: {theme}, Auto-clean: {auto_clean}")
        self.manager.current = 'home'

class AboutScreen(Screen):
    pass

# Create the screen manager
class PcCleanerApp(App):
    def build(self):
        Builder.load_string(KV)
        
        sm = ScreenManager()
        sm.add_widget(HomeScreen(name='home'))
        sm.add_widget(RegisterScreen(name='register'))
        sm.add_widget(LoginScreen(name='login'))
        sm.add_widget(CleaningScreen(name='cleaning'))
        sm.add_widget(ResultScreen(name='results'))
        sm.add_widget(SettingsScreen(name='settings'))
        sm.add_widget(AboutScreen(name='about'))
        
        return sm

if __name__ == '__main__':
    PcCleanerApp().run()
```

## Features Implemented:

1. **Screen Manager** with all requested screens:
   - HomeScreen (main menu)
   - RegisterScreen (user registration)
   - LoginScreen (user login)
   - CleaningScreen (main cleaning interface)
   - ResultScreen (cleaning results)
   - SettingsScreen (application settings)
   - AboutScreen (app information)

2. **Functionality**:
   - User registration and login (mock implementation)
   - File cleaning with options for:
     - Temporary files
     - Browser cache
     - System logs
     - Recycle bin
   - Settings management
   - Results display

3. **UI Elements**:
   - Buttons for navigation
   - Text inputs for forms
   - Checkboxes for options
   - Scrollable content areas
   - Themed buttons with different colors

## How to Run:

1. Save this code to a file named `main.py`
2. Install required dependencies:
   ```
   pip install kivy winshell
   ```
3. Run the application:
   ```
   python main.py
   ```

## Notes for Improvement:

1. **Images**: Create an `assets` folder and add a `cleaner.png` image for the home screen.

2. **Database**: For a real application, you'd want to:
   - Add proper user authentication
   - Store settings persistently
   - Save cleaning history

3. **Additional Features**:
   - Progress bars for cleaning operations
   - More detailed cleaning options
   - Scheduled cleaning
   - Disk space visualization
