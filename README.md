# Bareos Microsoft 365 Backup to AWS S3 Solution

## Overview

This project implements an **open-source Microsoft 365 backup solution** using **Bareos** as the backup engine with **AWS S3** as the storage backend. 

## 🎯 Project Goals

- Create a cost-effective alternative to commercial M365 backup solutions
- Leverage Bareos' enterprise backup capabilities with custom M365 integration
- Store backups efficiently in AWS S3
- Provide granular recovery options for M365 data


## 🏗️ Architecture

```
Microsoft 365 (Graph API)
    ↓
M365 Data Extractor (Python/PowerShell)
    ↓
Bareos Custom Plugin
    ↓
Bareos Core (Director/Storage/File Daemon)
    ↓
AWS S3 Storage Backend
```

### Key Components

1. **Bareos Core Infrastructure**
   - Director: Orchestrates backup jobs
   - Storage Daemon: Manages storage operations
   - File Daemon: Handles data transfer
   - Custom Plugin: M365 data source integration

2. **M365 Data Extractor**
   - Graph API client for data retrieval
   - Incremental backup support using delta queries
   - Multi-format data handling (JSON, PST, files)

3. **AWS S3 Backend**
   - Primary storage with lifecycle policies
   - Encryption at rest and in transit
   - Cross-region replication for DR

