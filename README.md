# Monocle Bridge

Bridges the proot CLI environment to the Brilliant Monocle via Web Bluetooth.

*Documentation: CC BY-SA 4.0. Code: AGPLv3. See [LICENSE](LICENSE) and [LICENSE.markdown](LICENSE.markdown).*

## How it works

```
[proot]                    [Android Chrome]              [Monocle]
  |                              |                           |
  |  monocle-cli repl "1+1"       |                           |
  |-------> WebSocket ----------->|  Web Bluetooth            |
  |         (port 8766)            |  -------> BLE ----------->|
  |                               |                           |
  |         <-------- response <--|  <------ notify ----------|
  |<------ WebSocket              |                           |
```

1. **server.py** runs in proot - HTTP on 8765 (serves bridge.html), WebSocket on 8766
2. **Chrome** on the Android device loads http://127.0.0.1:8765
3. The bridge page uses Web Bluetooth to connect to the Monocle
4. **monocle-cli** in proot sends commands over WebSocket; the bridge relays to/from the Monocle

## Setup

```bash
cd /root/monocle-bridge
apt install python3-websockets   # or: pip install websockets
python3 server.py               # leave running
```

On the **same Android device**, open Chrome and go to: **http://127.0.0.1:8765**

Click "Connect to Monocle" and select your device when prompted.

## Usage

```bash
# Connect (run once after opening bridge page)
python3 monocle-cli.py connect

# Run Python on the Monocle
python3 monocle-cli.py repl "1+1"
python3 monocle-cli.py repl "print('hello')"

# Pipe code
echo "import display; display.text('hi')" | python3 monocle-cli.py repl
```

## Testing

```bash
pytest tests/ -v
pytest tests/ --cov=. --cov-fail-under=80
```

## Requirements

- proot shares network with Android host (127.0.0.1 reaches the phone)
- Chrome on Android with Web Bluetooth support
- Monocle powered on and in range
