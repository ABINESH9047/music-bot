# Overview

MuseHeart MusicBot is a Discord music bot written in Python that provides interactive music playback with rich player controls, slash commands, and multiple visual themes (skins). The bot supports various music sources including YouTube, Spotify, SoundCloud, and more through Lavalink audio nodes. It features persistent player sessions, queue management, DJ roles, autoplay recommendations, and customizable player interfaces that can be displayed in normal or static (pinned) modes.

# User Preferences

Preferred communication style: Simple, everyday language.

# System Architecture

## Bot Framework
- **Discord Library**: Built on `disnake` (Discord API wrapper fork) with support for slash commands and message components
- **Command System**: Hybrid approach supporting both slash commands (`/play`, `/setup`) and prefix-based commands (`!!play`)
- **Bot Pooling**: Supports running multiple bot instances simultaneously through the `BotPool` class, allowing horizontal scaling with shared database and Lavalink nodes
- **Module System**: Cog-based architecture with separate modules for music playback, settings, error handling, help commands, and administrative features

## Audio Processing
- **Lavalink Integration**: Custom Wavelink client for connecting to Lavalink audio nodes (Java-based audio processing servers)
- **Multi-Source Support**: Handles YouTube, YouTube Music, Spotify, SoundCloud, and other sources through yt-dlp and Spotify API integration
- **Audio Filters**: Supports equalizers, distortion, timescale adjustments, and other audio effects through Lavalink filters
- **Local Lavalink**: Can automatically download and run a local Lavalink instance if no external nodes are configured

## Player System
- **LavalinkPlayer Model**: Stateful player instances per guild with queue management, autoplay, loop modes, and volume control
- **Player Sessions**: Persistent player state stored in database, allowing resumption after bot restarts
- **Skin System**: Pluggable UI themes for player controllers with both "normal" (temporary) and "static" (persistent) message modes
- **Queue Management**: Support for tracks, playlists, favorite links, and autoplay recommendations based on current track
- **Controller Modes**: Interactive button-based controls (play/pause, skip, volume, shuffle) with customizable layouts per skin

## Database Architecture
- **Dual Database Support**: 
  - MongoDB (production): Using Motor async driver for scalable cloud database
  - TinyDB/TinyMongo (development): File-based JSON storage for local testing
- **Data Models**: Separate collections for guild settings, user preferences, and global configuration
- **Caching Layer**: Local JSON file cache in `.db_cache/` for reduced database queries
- **Schema Versioning**: Migration system tracks schema versions for backward compatibility

## Permission & Access Control
- **Role-Based Access**: DJ roles for privileged music commands, with fallback to voice channel management permissions
- **Vote/Request System**: Non-DJ users can request skips/stops with majority vote from voice channel members
- **Channel Restrictions**: Configurable song request channels and player controller channels per guild
- **Cooldown System**: Rate limiting on commands to prevent spam and abuse

## Integration Services
- **Spotify API**: Spotipy library for resolving Spotify tracks/playlists/albums to searchable queries
- **Web Interface**: Tornado-based websocket server for RPC (Rich Presence) integration and web dashboard
- **Error Reporting**: Optional webhook integration for automatic error reporting to designated Discord channels

## Configuration Management
- **Environment Variables**: Primary configuration through `.env` files or system environment variables
- **Multi-Bot Tokens**: Support for multiple bot tokens (`TOKEN_BOT_1`, `TOKEN_BOT_2`) for load distribution
- **Dynamic Config**: Runtime configuration changes stored in database per-guild and per-user
- **Feature Flags**: Toggle features like message content intent, command cooldowns, and auto-sync through environment variables

## Deployment Support
- **Railway/Replit Ready**: Preconfigured deployment files (`railway.json`, Replit compatibility)
- **Process Management**: Graceful shutdown handling, automatic reconnection to Lavalink nodes with exponential backoff
- **Health Monitoring**: Stats tracking for CPU, memory, player count, and node performance with penalty-based load balancing

# External Dependencies

## Core Services
- **MongoDB Atlas**: Primary database for production deployments (connection via `MONGO` environment variable)
- **Lavalink Nodes**: External audio processing servers, auto-downloaded from configured server list URL or run locally
- **Spotify Developer API**: Required for Spotify track resolution (`SPOTIFY_CLIENT_ID`, `SPOTIFY_CLIENT_SECRET`)

## Python Libraries
- **disnake**: Discord API wrapper with slash command support
- **wavelink**: Custom Lavalink client library (included in repository)
- **yt-dlp**: YouTube and general media extraction
- **spotipy**: Spotify API client
- **motor**: Async MongoDB driver
- **aiohttp**: Async HTTP client for API requests
- **tornado**: Websocket server for RPC integration

## Optional Services
- **Discord Webhooks**: For error reporting and bot add/remove logging
- **Git Repository**: Source code updates and version tracking