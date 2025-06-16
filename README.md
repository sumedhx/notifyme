# NotifyMe: A Terminal and Mobile Notification Tool

NotifyMe is a simple Bash utility that sends mobile notifications about the success or failure of terminal commands. This guide will walk you through the setup and usage of NotifyMe, making it easier to stay informed about your terminal tasks.

---

## Features

- Receive notifications on your mobile device when terminal commands complete.
- Distinguish between successful and failed tasks.
- Works seamlessly with Pushbullet for mobile notifications.

---

## Requirements

1. **Operating System**: macOS or Linux.
2. **Dependencies**:
   - `curl`: For sending HTTP requests.
   - Pushbullet account and API key.
3. **Installed Software**:
   - Pushbullet CLI (`pb`) or equivalent notification service.

---

## Setup Guide

### 1. Install Pushbullet CLI

If you haven’t already installed the Pushbullet CLI:

```bash
brew install --cask pb
```

### 2. Set Up NotifyMe Function

Add the following function to your shell configuration file (e.g., `~/.bashrc`, `~/.zshrc`, or `~/.bash_profile`):

```bash
notifyme() {
    local pb="/Users/sumedh/Library/Python/3.9/bin/pb"  

    # Handle direct message mode
    if [[ "$1" == "-m" && -n "$2" ]]; then
        local msg="$2"
        PYTHONWARNINGS=ignore "$pb" push "$msg"
        return
    fi

    # Otherwise, run the given command
    "$@"
    local status=$? #The Status code of the failed command
    local cmd="$*"
    
    PYTHONWARNINGS=ignore "$pb" push "$(
        if [ $status -eq 0 ]; then
            echo "✅ [Task Completed!]::  $cmd"
        else
            echo "❌ [Task Failed!]::  $cmd"
        fi
    )"
}

```

Reload your shell configuration:

```bash
source ~/.zshrc  # or the appropriate file for your shell
```

### 3. Obtain Pushbullet API Key

1. Log in to your Pushbullet account.
2. Go to [Pushbullet API Settings](https://www.pushbullet.com/#settings) and create an access token.
3. Save the token for later use (the `pb` CLI uses it automatically if configured).

### 4. Test Pushbullet Notifications

Run this command to test notifications:

```bash
pb push "Test notification from NotifyMe!"
```

---

## Usage Guide

### Basic Syntax

```bash
notifyme <command>
```

### Example Usage

#### Successful Command

```bash
notifyme ls
```

- **Notification**: ✅ Task 'ls' completed!

#### Failed Command

```bash
notifyme ls nonexistent_folder
```

- **Notification**: ❌ Task 'ls nonexistent\_folder' failed!

---

## How NotifyMe Works

1. The `notifyme` function runs the provided command.
2. It checks the command’s exit status (`$?`):
   - **Success** (exit status `0`): Sends a success notification.
   - **Failure** (non-zero exit status): Sends a failure notification.

---

## Notes

- NotifyMe supports chaining commands:
  ```bash
  notifyme "echo Hello && echo World"
  ```
- Avoid using commands that require user input, as NotifyMe works best with commands that complete automatically.

---

## Troubleshooting

1. **No Notifications Received**:
   - Ensure the Pushbullet CLI (`pb`) is installed and working.
   - Verify your Pushbullet API key.
2. **Command Errors**:
   - Double-check your command syntax.

---

## Contributing

Contributions are welcome! Feel free to fork this repository and submit a pull request.

---

## License

This project is licensed under the MIT License.

