# MLX Whisper MCP Server

A simple Model Context Protocol (MCP) server that provides audio transcription capabilities using MLX Whisper on Apple Silicon Macs.

## Features

- Transcribe audio files directly from disk
- Transcribe audio from base64-encoded data
- Uses the high-quality `mlx-community/whisper-large-v3-turbo` model
- Self-contained script with automatic dependency management via `uv run`
- Rich console output for easy debugging

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

## Using with Claude Desktop

1. Edit your Claude Desktop configuration file:

```bash
# On macOS:
code ~/Library/Application\ Support/Claude/claude_desktop_config.json

# On Windows:
code %APPDATA%\Claude\claude_desktop_config.json
```

2. Add the MLX Whisper MCP server configuration:

```json
{
  "mcpServers": {
    "mlx-whisper": {
      "command": "uv",
      "args": [
        "--directory",
        "/absolute/path/to/mlx_whisper_mcp/",
        "run",
        "mlx_whisper_mcp.py"
      ]
    }
  }
}

3. Restart Claude Desktop

## Available Tools

The server provides two main tools:

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

## Example Prompts for Claude

- "Transcribe the audio file at /Users/username/Desktop/recording.mp3"
- "Translate this Spanish audio recording to English" (when uploading an audio file)
- "What is being said in this recording?" (when uploading an audio file)

## Troubleshooting

- **Import Error**: If you see an error about MLX Whisper not being found, make sure you're running on an Apple Silicon Mac
- **File Not Found**: Make sure you're using absolute paths when referencing audio files
- **Memory Issues**: Very long audio files may cause memory pressure with the large model

## How It Works

This server uses the MCP Python SDK to expose MLX Whisper's transcription capabilities to clients like Claude. When a transcription is requested:

1. The audio data is received (either as a file path or base64-encoded data)
2. For base64 data, a temporary file is created
3. MLX Whisper is used to perform the transcription
4. The transcription text is returned to the client
5. Temporary files are cleaned up

## License

Apache License 2.0
See [LICENSE](LICENSE) for details.