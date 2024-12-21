# GRE Word Reviewer with Voice Assistance

This README is a comprehensive guide to understanding, using, and customizing the GRE Word Reviewer Python program. I came up with this code when I was frustrated during the word reviewing process in my GRE Exam. The program is designed to help users enhance their vocabulary memorization by combining text-to-speech (TTS), interactive input, and progress tracking. It is particularly useful for GRE preparation but can be adapted for any vocabulary-building purpose. Below, we’ll walk through **how the code works**, **how to use it**, and **how to customize it** to suit your needs.

---

## Overview of the Code

The program is divided into multiple functional sections, each serving a specific purpose. This section explains what each part of the code does, how it works, and how you can modify it.

---

### 1. **Imports**

```python
import pyttsx3
import time
import keyboard  
import csv
import random
import pyperclip
```

- **`pyttsx3`**: A text-to-speech conversion library that allows the program to "speak" words aloud.
- **`time`**: Used to introduce sleep delays for smoother operation and prevent repeated key detections.
- **`keyboard`**: Handles keyboard input for interactive word review.
- **`csv`**: Enables reading and writing from/to CSV files to manage word lists.
- **`random`**: Used to shuffle the word list for randomization.
- **`pyperclip`**: Copies the current word to the clipboard for quick access.

> **How to Customize:**  
   - If you don’t need clipboard integration, you can remove the `pyperclip` import and comment out its usage in the code.
   - If you want to add more libraries (e.g., for GUI or API integration), you can add them here.

---

### 2. **`read_words()` Function**

```python
def read_words(words, original_csv_path, new_csv_path):
    engine = pyttsx3.init()

    # Change the voice (female or male)
    voices = engine.getProperty('voices')
    engine.setProperty('voice', voices[0].id)  # use voices[0].id for male

    # Adjusting the rate and volume
    engine.setProperty('rate', 120)   # Speed of speech
    engine.setProperty('volume', 0.9)  # Volume level (0.0 to 1.0)
    
    n = 0
    words = random.sample(words, len(words))  # Shuffle the word list
    
    for word in words: 
        n += 1
        print(f"{n} Word: {word}")
        engine.say(word)
        engine.runAndWait()
        pyperclip.copy(word)  # Copy the word to the clipboard

        while True:
            if keyboard.is_pressed('+'):  # Mark as memorized
                add_word_to_new_csv(word, new_csv_path)  # Move to new CSV
                remove_word_from_csv(word, original_csv_path)  # Remove from original CSV
                time.sleep(0.1)  # Prevent multiple key detections
                break
            elif keyboard.is_pressed('-'):  # Skip the word
                time.sleep(0.1)
                break
```

#### **What It Does:**
- Initializes the **text-to-speech engine** (`pyttsx3`) and sets its properties (voice, rate, volume).
- Shuffles the list of words to ensure random order.
- Iterates through the words, displaying them on the screen, speaking them aloud, and copying each to the clipboard.
- Waits for user input (`+` or `-`) to determine whether the word is memorized or skipped:
  - **`+`**: Moves the word to the "memorized" CSV and removes it from the original list.
  - **`-`**: Skips the word, leaving it in the original list for future review.

#### **How to Customize:**
1. **Change Voice**:
   - The program uses `voices[0].id` (default male voice). To switch to female or another available voice:
     ```python
     engine.setProperty('voice', voices[1].id)  # Change the index to test other voices
     ```

2. **Adjust Speed and Volume**:
   - To change the speed of speech:
     ```python
     engine.setProperty('rate', 150)  # Increase for faster speech
     ```
   - To adjust volume:
     ```python
     engine.setProperty('volume', 0.8)  # Set between 0.0 and 1.0
     ```

3. **Change Key Bindings**:
   - To use different keys instead of `+` and `-`:
     ```python
     if keyboard.is_pressed('your_new_key'):  # Replace 'your_new_key' with your preferred key
     ```

---

### 3. **`read_words_from_csv()` Function**

```python
def read_words_from_csv(file_path):
    words = []
    with open(file_path, newline='', encoding='utf-8') as csvfile:
        reader = csv.reader(csvfile)
        for row in reader:
            words.append(row[0])  # Extract the first column (word)
    return words
```

#### **What It Does:**
- Reads all words from the provided CSV file (e.g., `old.csv`) and returns them as a list.

#### **How to Customize:**
- Ensure your CSV file contains only one word per row in the first column.
- If your file has additional columns (e.g., definitions), you can modify the code to extract those too:
  ```python
  for row in reader:
      words.append((row[0], row[1]))  # Extract word and definition
  ```

---

### 4. **`remove_word_from_csv()` Function**

```python
def remove_word_from_csv(word, file_path):
    with open(file_path, 'r', newline='', encoding='utf-8') as csvfile:
        reader = csv.reader(csvfile)
        words = list(reader)
    
    words = [row for row in words if row[0] != word]
    
    with open(file_path, 'w', newline='', encoding='utf-8') as csvfile:
        writer = csv.writer(csvfile)
        writer.writerows(words)
```

#### **What It Does:**
- Removes a specific word from the original CSV file.

#### **How to Customize:**
- You can modify this function to log removed words or create a backup of the CSV file before editing.

---

### 5. **`add_word_to_new_csv()` Function**

```python
def add_word_to_new_csv(word, file_path):
    with open(file_path, 'a', newline='', encoding='utf-8') as csvfile:
        writer = csv.writer(csvfile)
        writer.writerow([word])
```

#### **What It Does:**
- Appends the memorized word to the new CSV file (e.g., `memorized.csv`).

#### **How to Customize:**
- If you want to include additional data (e.g., timestamp), modify the function:
  ```python
  from datetime import datetime
  writer.writerow([word, datetime.now().strftime('%Y-%m-%d %H:%M:%S')])
  ```

---

### 6. **Main Program Execution**

```python
if __name__ == "__main__":
    original_csv_path = 'C:/Users/win10/OneDrive/Desktop/old.csv'
    new_csv_path = 'C:/Users/win10/OneDrive/Desktop/memorized.csv'
    
    words = read_words_from_csv(original_csv_path)
    read_words(words, original_csv_path, new_csv_path)
```

#### **What It Does:**
- Defines file paths for the original and memorized word lists.
- Reads the words from the original CSV and starts the review process.

#### **How to Customize:**
- Update the file paths to match your system:
  ```python
  original_csv_path = 'path/to/your/old.csv'
  new_csv_path = 'path/to/your/memorized.csv'
  ```

---

## How to Use the Program

1. **Prepare Your Word Lists**:
   - Create a CSV file (`old.csv`) with one word per row.
   - Optionally, create an empty CSV file (`memorized.csv`) for storing memorized words.

2. **Run the Program**:
   ```
   python gre_word_reviewer.py
   ```

3. **Interact with the Program**:
   - **`+`**: Mark a word as memorized.
   - **`-`**: Skip the word.

4. **Check Your Progress**:
   - Open `memorized.csv` to see the words you've mastered.
   - Open `old.csv` to review the remaining words.

---

## Final Words

This program is a simple yet powerful tool to help you memorize vocabulary faster. By combining auditory, visual, and interactive learning techniques, it makes the process fun and efficient. Customize it as much as you like, and feel free to share your improvements!

Happy learning! 🎓
