# MLX Whisper MCP Server

A simple Model Context Protocol (MCP) server that provides audio transcription capabilities using MLX Whisper on Apple Silicon Macs.

## Features

- Transcribe audio files directly from disk
- Transcribe audio from base64-encoded data
- Download and transcribe YouTube videos
- Uses the high-quality `mlx-community/whisper-large-v3-turbo` model
- Self-contained script with automatic dependency management via `uv run`
- Rich console output for easy debugging
- Saves transcription text files alongside audio files

## Requirements

- Python 3.12 or higher
- Apple Silicon Mac (M-series)
- `uv` installed (`pip install uv` or `curl -sS https://astral.sh/uv/install.sh | bash`)

## Quick Start

Run directly with `uv run`:

```bash
uv run mlx_whisper_mcp.py
```

That's it! The script will automatically install its own dependencies and start the MCP server.
Note: The first time you run the script, it may take longer to start as it will download the Whisper model (approx. 1.6GB). Subsequent runs will be faster.

## Using with Claude Desktop

There are two main ways to integrate this server with Claude Desktop:

### Option 1: Using `uv` (Recommended)

1.  Navigate to the directory where you've cloned or saved `mlx_whisper_mcp.py`.
2.  Run the following command:
    ```bash
    uv tool run fastmcp install mlx_whisper_mcp.py
    ```
3.  Restart Claude Desktop if it was running. `fastmcp` will have set up the necessary configuration to launch the server, including handling its dependencies via `uv run`.

### Option 2: Manual Configuration

If you prefer to configure Claude Desktop manually:

1. Edit your Claude Desktop configuration file:

   ```bash
   # On macOS:
   code ~/Library/Application\ Support/Claude/claude_desktop_config.json

   # On Windows:
   code %APPDATA%\Claude\claude_desktop_config.json
   ```

2. Add the MLX Whisper MCP server configuration.
   **Important:** Replace `/absolute/path/to/mlx_whisper_mcp/` in the `cwd` field below with the actual absolute path to the directory containing `mlx_whisper_mcp.py` on your system.

   ```json
   {
     "mcpServers": {
       "mlx-whisper": {
         "command": "uv",
         "args": [
           "run",
           "mlx_whisper_mcp.py"
         ],
         "cwd": "/absolute/path/to/mlx_whisper_mcp/"
       }
     }
   }
   ```
   This configuration tells Claude Desktop to execute `mlx_whisper_mcp.py` using `uv run`, with the current working directory (`cwd`) set to the script's location. `uv run` will handle installing the dependencies defined for the script.

3. Restart Claude Desktop.


## Available Tools

The server provides the following tools:

### 1. `transcribe_file`

Transcribes an audio file from a path on disk.

Parameters:
- `file_path`: Path to the audio file
- `language`: (Optional) Language code to force a specific language
- `task`: "transcribe" or "translate" (translates to English)

### 2. `transcribe_audio`

Transcribes audio from base64-encoded data.

Parameters:
- `audio_data`: Base64-encoded audio data
- `language`: (Optional) Language code to force a specific language
- `file_format`: Audio file format (wav, mp3, etc.)
- `task`: "transcribe" or "translate" (translates to English)

### 3. `download_youtube`

Downloads a YouTube video.

Parameters:
- `url`: YouTube video URL
- `keep_file`: If True, keeps the downloaded file (default: True)

### 4. `transcribe_youtube`

Downloads and transcribes a YouTube video.

Parameters:
- `url`: YouTube video URL
- `language`: (Optional) Language code to force a specific language
- `task`: "transcribe" or "translate" (translates to English)
- `keep_file`: If True, keeps the downloaded file (default: True)

## Example Prompts for Claude Desktop

- "Transcribe the audio file at /Users/username/Desktop/recording.mp3"
- "Translate this Spanish audio recording to English" (when uploading an audio file)
- "What is being said in this recording?" (when uploading an audio file)
- "Download and transcribe this YouTube video: https://www.youtube.com/watch?v=dQw4w9WgXcQ"
- "Download this YouTube video: https://www.youtube.com/watch?v=dQw4w9WgXcQ"

## How It Works

This server uses the MCP Python SDK to expose MLX Whisper's transcription capabilities to clients like Claude. When a transcription is requested:

1. The audio data is received (either as a file path, base64-encoded data, or YouTube URL)
2. For YouTube URLs, the video is downloaded to `~/.mlx-whisper-mcp/downloads`
3. For base64 data, a temporary file is created
4. MLX Whisper is used to perform the transcription
5. The transcription text is saved to a .txt file alongside the audio file
6. The transcription text is returned to the client
7. Temporary files are cleaned up (unless keep_file=True)

## Troubleshooting

- **Import Error**: If you see an error about MLX Whisper not being found, make sure you're running on an Apple Silicon Mac
- **File Not Found**: Make sure you're using absolute paths when referencing audio files
- **Memory Issues**: Very long audio files may cause memory pressure with the large model
- **YouTube Download Errors**: Some videos may be restricted or require authentication
- **JSON Errors**: If you see "not valid JSON" errors in logs, make sure server logging output is properly directed to stderr

## License

Apache License 2.0
See [LICENSE](LICENSE) for details.
