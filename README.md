Here’s a description for your **File Integrity Checker Tool** with the main code parts highlighted, which you can use for your GitHub repository `README.md`:

---

# File Integrity Checker Tool

## Description

The **File Integrity Checker Tool** is a Python-based utility that helps in monitoring and ensuring the integrity of files by checking their hash values. It calculates the hash of monitored files and stores them in a local JSON database. When a file is modified, the tool compares the file’s current hash with the previously stored hash and sends a pop-up notification if any modification is detected.

This tool can be used to track changes in files, especially for use cases where file integrity is critical (e.g., configuration files, logs, or code files). It also provides the ability to manually update file hashes and remove files from the monitoring database.

### Key Features:
- **File Hash Calculation**: Uses MD5 hashing to detect file modifications.
- **Pop-up Notifications**: Shows notifications for any modified files.
- **Database Management**: Stores file paths, hashes, and timestamps in a JSON database.
- **Continuous Monitoring**: Periodically checks for file integrity and updates the database with new hashes.
- **Manual Operations**: Allows adding/removing files, and manually updating file hashes through an interactive CLI.

---

## Main Code Parts

### 1. **Database Management**:
This part of the code manages a local JSON database (`hash_database.json`) to store file paths, their corresponding hash values, and timestamps.

```python
DATABASE_FILE = "hash_database.json"

# Load existing database or create a new one
def load_database():
    if os.path.exists(DATABASE_FILE):
        with open(DATABASE_FILE, "r") as f:
            return json.load(f)
    return {}

# Save the updated database
def save_database(database):
    with open(DATABASE_FILE, "w") as f:
        json.dump(database, f, indent=4)
```

### 2. **File Hash Calculation**:
The tool calculates the hash of files using the MD5 algorithm. This is done to detect any changes in the file content.

```python
def calculate_file_hash(filepath):
    hash_md5 = hashlib.md5()
    try:
        with open(filepath, "rb") as f:
            for chunk in iter(lambda: f.read(4096), b""):
                hash_md5.update(chunk)
        return hash_md5.hexdigest()
    except FileNotFoundError:
        print(f"File not found: {filepath}")
        return None
```

### 3. **File Integrity Check**:
This function compares the current hash of each file with its stored hash in the database. If a mismatch is found, it triggers a pop-up notification.

```python
def check_file_integrity(database):
    for filepath, entries in database.items():
        last_saved_hash = entries[-1]["hash"]  # Get the last saved hash
        current_hash = calculate_file_hash(filepath)

        if current_hash is None:
            continue  # Skip if the file is not found

        if current_hash != last_saved_hash:
            print(f"Change detected in {filepath}. Showing notification.")
            show_notification(filepath)  # Show a notification for the modified file
            time.sleep(60)  # Wait 1 minute before checking again
            current_hash = calculate_file_hash(filepath)  # Recalculate hash

        # Update the hash in the database if it has changed
        if current_hash != entries[-1]["hash"]:
            timestamp = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
            new_entry = {"hash": current_hash, "timestamp": timestamp}
            database[filepath].append(new_entry)
            print(f"Updated hash for {filepath} in database.")
```

### 4. **Notification System**:
When a file modification is detected, the tool displays a pop-up notification using Tkinter. This is done for each modified file.

```python
def show_notification(filepath):
    root = Tk()
    root.withdraw()  # Hide the root window
    messagebox.showinfo("File Integrity Alert", f"File modified: {filepath}")
    root.destroy()  # Close the Tkinter instance after the message is displayed
```

### 5. **Main Loop**:
The main loop continuously monitors files in the database for modifications, checks their integrity, and updates the database with the new hashes.

```python
def main():
    database = load_database()
    if not database:
        print("No files to monitor.")
        return

    try:
        while True:
            print("Monitoring files...")  # Message to show monitoring is active
            check_file_integrity(database)
            save_database(database)
            time.sleep(30)  # Wait for 30 seconds before the next check
    except KeyboardInterrupt:
        print("Monitoring stopped.")
```

---

## How to Use:

### Prerequisites:
- Python 3.x
- Tkinter (for pop-up notifications)

### Installation:

1. Clone the repository:
    ```bash
    git clone https://github.com/yourusername/file-integrity-checker.git
    cd file-integrity-checker
    ```

2. Install dependencies (if required):
    ```bash
    pip install -r requirements.txt
    ```

3. Ensure the `hash_database.json` file exists, or the program will create it on its own.

### Running the Program:

- **Add files to monitor**:
  Run the script in interactive mode to add files to the monitoring list.
  
- **Monitor file integrity**:
  Start the program to continuously check for file integrity and display notifications for any file modifications.
  
    ```bash
    python int.py
    ```

---

## License:
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

You can paste this description in your `README.md` file to give others a clear overview of the tool and its functionality. If you want to modify the formatting or add more details, feel free to adjust as needed!
