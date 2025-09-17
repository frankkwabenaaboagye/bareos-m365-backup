# Bareos Fundamentals Exploration

## What is Bareos?

Bareos (Backup Archiving Recovery Open Sourced) is an enterprise-grade backup solution that consists of three main components working together to provide comprehensive backup and recovery capabilities.

## Core Architecture

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Bareos        │    │   Bareos        │    │   Bareos        │
│   Director      │◄──►│   Storage       │◄──►│   File          │
│   (Management)  │    │   Daemon        │    │   Daemon        │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         │                       │                       │
         ▼                       ▼                       ▼
  Job Scheduling           Storage Backend         Client Data
  Catalog Database        (Disk/Cloud/Tape)      (Files/Apps)
```

## Core Components

### 1. Bareos Director (bareos-dir)
**Role**: The "brain" of the backup system
- **Job Management**: Schedules and orchestrates backup/restore jobs
- **Catalog Database**: Maintains metadata about all backups (PostgreSQL/MySQL)
- **Client Management**: Manages connections to File Daemons
- **Policy Enforcement**: Applies retention policies, schedules, etc.
- **API**: Provides REST API and command-line interface

**Configuration File**: `bareos-dir.conf`

### 2. Bareos Storage Daemon (bareos-sd) 
**Role**: Handles the physical storage of backup data
- **Storage Backend**: Interfaces with various storage types (disk, cloud, tape)
- **Data Transfer**: Receives data from File Daemons and stores it
- **Compression**: Can compress data before storage
- **Encryption**: Can encrypt data at rest
- **Multi-volume**: Manages spanning across multiple volumes/media

**Configuration File**: `bareos-sd.conf`

### 3. Bareos File Daemon (bareos-fd)
**Role**: Runs on client machines to backup data
- **Data Collection**: Gathers files and application data to backup
- **Compression**: Can compress data before transmission
- **Encryption**: Can encrypt data in transit
- **Plugins**: Supports plugins for specialized data sources
- **Incremental Logic**: Tracks changes for incremental backups

**Configuration File**: `bareos-fd.conf`

## Key Concepts

### Jobs
- **Backup Jobs**: Define what data to backup, when, and where
- **Restore Jobs**: Define what data to restore and where
- **Job Types**: Full, Incremental, Differential

### Resources
- **Client**: Represents a machine with a File Daemon
- **FileSet**: Defines what files/directories to backup
- **Schedule**: Defines when jobs run
- **Pool**: Groups of storage volumes
- **Storage**: Defines where backups are stored

### Plugins
- **Purpose**: Extend Bareos to backup specialized data sources
- **Types**: Python, C++, or external command plugins
- **Examples**: Database plugins (MySQL, PostgreSQL), VMware, etc.

## How It Works - Backup Process

1. **Director** schedules a backup job
2. **Director** contacts the **File Daemon** on the client
3. **File Daemon** reads the data according to the FileSet
4. **File Daemon** sends data to the **Storage Daemon**
5. **Storage Daemon** writes data to the configured storage backend
6. **Director** records metadata in the catalog database

## Plugin Integration Points

For Microsoft 365 integration, we need to focus on the **File Daemon plugins**:

### Plugin Types Available

1. **Python Plugin (fd-python-plugin)**
   - Write plugins in Python
   - Access to Bareos plugin API
   - Good for API-based data sources like M365

2. **Command Plugin (fd-command-plugin)**
   - Execute external commands to generate backup data
   - Simpler but less integrated

3. **C++ Plugin (custom)**
   - Most powerful but requires C++ development
   - Full access to Bareos internals

## Microsoft 365 Integration Strategy

Based on this architecture, here's how we can integrate M365:

### Option 1: Python Plugin (Recommended)
```python
# Plugin runs on File Daemon
# Calls Microsoft Graph API
# Presents data to Bareos as "files"
M365 Graph API → Python Plugin → Bareos File Daemon → Storage Daemon → AWS S3
```

### Option 2: Command Plugin 
```bash
# External script handles M365 API calls
# Script outputs data stream to Bareos
M365 API Script → Command Plugin → Bareos File Daemon → Storage Daemon → AWS S3
```

## Next Steps for Exploration

1. **Install Bareos locally** to understand the components
2. **Test basic file backup** to see how it works
3. **Configure S3 storage backend** to understand cloud storage
4. **Create a simple Python plugin** to understand the plugin interface
5. **Test with sample M365 data** to validate the approach

## Questions to Answer

1. How does the Python plugin interface work?
2. How does Bareos handle incremental backups at the plugin level?
3. How do we present M365 data (emails, files, etc.) as "files" to Bareos?
4. How do we handle M365 API rate limiting in plugins?
5. How do we configure S3 storage with proper encryption and lifecycle policies?

## Installation Options

### Windows (Our Current Environment)
- Bareos Windows client/server packages
- Can run all components on Windows
- Good for development/testing

### Linux (Production Recommended)
- Better performance for production
- More mature Docker support
- Better plugin ecosystem

Let's start with a local Windows installation to understand the basics!