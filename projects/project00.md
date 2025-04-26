# Cleaning Up Junk Files on C: Drive Using Python

## Method 1: Using built-in Windows utilities via Python

```python
import os
import subprocess
import shutil
import tempfile

def clean_windows_junk():
    try:
        # Run Disk Cleanup utility
        subprocess.run(['cleanmgr', '/sagerun:1'], check=True)
        
        # Clear temporary files
        temp_dir = tempfile.gettempdir()
        for root, dirs, files in os.walk(temp_dir):
            for file in files:
                try:
                    os.unlink(os.path.join(root, file))
                except Exception as e:
                    print(f"Couldn't delete {file}: {e}")
        
        # Clear Windows temporary files
        win_temp = os.path.join(os.environ['SystemRoot'], 'Temp')
        for root, dirs, files in os.walk(win_temp):
            for file in files:
                try:
                    os.unlink(os.path.join(root, file))
                except Exception as e:
                    print(f"Couldn't delete {file}: {e}")
        
        # Clear user temporary files
        user_temp = os.path.join(os.environ['USERPROFILE'], 'AppData', 'Local', 'Temp')
        shutil.rmtree(user_temp, ignore_errors=True)
        
        # Clear browser cache (example for Chrome)
        chrome_cache = os.path.join(os.environ['USERPROFILE'], 'AppData', 'Local', 
                                   'Google', 'Chrome', 'User Data', 'Default', 'Cache')
        shutil.rmtree(chrome_cache, ignore_errors=True)
        
        # Clear recycle bin (requires admin privileges)
        try:
            from winshell import recycle_bin
            recycle_bin().empty(confirm=False, show_progress=False, sound=False)
        except ImportError:
            print("Install winshell module to empty recycle bin: pip install winshell")
        
        print("Junk files cleaned successfully!")
        
    except Exception as e:
        print(f"Error during cleanup: {e}")

if __name__ == "__main__":
    clean_windows_junk()
```

## Method 2: More advanced cleanup with specific targets

```python
import os
import shutil
import glob

def advanced_cleanup():
    # List of common junk file locations
    junk_locations = [
        # Windows temporary files
        r'C:\Windows\Temp\*',
        r'C:\Windows\Prefetch\*',
        
        # User temporary files
        os.path.join(os.environ['USERPROFILE'], 'AppData', 'Local', 'Temp', '*'),
        
        # Browser caches
        os.path.join(os.environ['USERPROFILE'], 'AppData', 'Local', 'Microsoft', 'Windows', 'INetCache', '*'),
        os.path.join(os.environ['USERPROFILE'], 'AppData', 'Local', 'Google', 'Chrome', 'User Data', 'Default', 'Cache', '*'),
        
        # Thumbnail cache
        os.path.join(os.environ['USERPROFILE'], 'AppData', 'Local', 'Microsoft', 'Windows', 'Explorer', 'thumbcache_*.db'),
        
        # Windows error reports
        os.path.join(os.environ['USERPROFILE'], 'AppData', 'Local', 'Microsoft', 'Windows', 'WER', '*'),
        
        # Software distribution downloads
        r'C:\Windows\SoftwareDistribution\Download\*'
    ]
    
    for location in junk_locations:
        try:
            files = glob.glob(location)
            for file in files:
                try:
                    if os.path.isfile(file):
                        os.unlink(file)
                    elif os.path.isdir(file):
                        shutil.rmtree(file, ignore_errors=True)
                    print(f"Deleted: {file}")
                except Exception as e:
                    print(f"Couldn't delete {file}: {e}")
        except Exception as e:
            print(f"Error processing {location}: {e}")

if __name__ == "__main__":
    advanced_cleanup()
```

## Important Notes:

1. **Run as Administrator**: Some locations require admin privileges to clean. Run your script as Administrator.

2. **Dependencies**: You may need to install some packages:
   ```
   pip install winshell
   ```

3. **Safety**: 
   - Always test on non-critical files first
   - Consider creating backups before major cleanups
   - Some "junk" files might be in use by the system

4. **Alternative**: You can also use Python to call the Windows Disk Cleanup tool more directly:
   ```python
   subprocess.run('cleanmgr /sagerun:1', shell=True)
   ```

