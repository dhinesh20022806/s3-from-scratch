# S3 Clone — Object Storage System in Go

A learning-oriented implementation of an S3-like object storage system built with Go.

This project explores the engineering challenges behind object storage systems, including concurrent request processing, filesystem internals, object consistency, and storage architecture.

**Current implementation: Single-machine object storage.**

The primary focus is understanding how an object storage system works internally, from handling concurrent uploads to persisting object data on disk. Distributed storage capabilities are part of the future roadmap.

## Demo

Watch the project demonstration:

[View Demo Video](./docs/demo.mp4)

## Features

- **Bucket Management** — Create buckets to organize stored objects.
- **Object Upload** — Upload objects through HTTP APIs.
- **Object Download** — Retrieve stored objects.
- **Multipart Upload** — Upload large objects in multiple parts.
- **Concurrent Write Processing** — Use Go goroutines and channels to process incoming write requests concurrently.
- **Dedicated File Allocation** — Explore allocating separate files to processing workers to reduce write contention and coordinate access to storage.

## Implementation Status

| Component                   | Status      |
| --------------------------- | ----------- |
| Bucket Management           | Implemented |
| Object Upload               | Implemented |
| Object Download             | Implemented |
| Concurrent Write Processing | Implemented |
| Dedicated File Allocation   | Implemented |
| Single-Machine Storage      | Implemented |
| Multipart Upload            | Planned     |
| Distributed Storage Nodes   | Planned     |
| Consistent Hashing          | Planned     |
| Data Redistribution         | Planned     |
| Replication and Recovery    | Planned     |

## Architecture

The current implementation follows a single-machine storage architecture.

It consists of an API layer, concurrent request processing, filesystem-based object storage, and PostgreSQL metadata management.

### Request Flow

1. A client sends an HTTP request to the API server.
2. The server validates the request and identifies the target bucket and object.
3. Upload requests are dispatched to the storage processing layer.
4. Go goroutines process incoming requests, coordinating through channels where appropriate.
5. The storage layer persists object data to the local filesystem.
6. PostgreSQL stores the relevant object metadata.
7. Download requests retrieve object data through the API server.

**Note:** Object data is currently stored on a single machine. Distributed storage-node placement and cross-node data management have not yet been implemented.

## Concurrency and Write Handling

One of the primary learning objectives is understanding how to handle concurrent object uploads.

The project explores using Go goroutines and channels to coordinate write operations and allocating dedicated files to processing workers.

This provides an opportunity to study:

- Concurrent request processing.
- Write contention and serialization.
- Coordination between workers.
- Safe access to shared metadata.
- Trade-offs between parallelism and storage consistency.

Dedicated files can reduce contention when workers write to independent files. However, this alone does not guarantee object-level consistency.

The implementation must also ensure that concurrent requests targeting the same object follow a defined consistency policy.

## Multipart Upload

Multipart upload allows large objects to be uploaded as multiple smaller parts.

The workflow includes:

1. Initiating a multipart upload.
2. Assigning an upload ID.
3. Uploading individual parts.
4. Tracking uploaded parts and their metadata.
5. Completing the upload by assembling the parts into the final object.
6. Handling incomplete uploads and abandoned parts.

This feature explores large-file handling, upload state management, and temporary storage lifecycle management.

## Tech Stack

### Client

- Go CLI

### Backend

- Go
- Gin

### Storage and Communication

- PostgreSQL — Object metadata and storage-related records.
- REST API — Client-facing operations.
- gRPC — Service-to-service communication where applicable.
- Linux Filesystem — Persistent object data storage.

## Lessons Learned

### 1. Go Concurrency

Explored how goroutines and channels coordinate concurrent work, distribute requests, and help manage storage operations.

### 2. Write Serialization

Learned why concurrent writes require explicit coordination when multiple requests can modify the same object or shared metadata.

### 3. Linux Filesystem Internals

Studied how Linux represents files using inodes, which store metadata such as file permissions, ownership, timestamps, and references to file data blocks.

This provided a deeper understanding of how object data is persisted on disk and how filesystem operations relate to storage-system design.

### 4. Object Storage Design

Explored the separation between object data and metadata, along with the challenges of uploading, retrieving, and organizing objects.

### 5. Multipart Upload

Investigated how large objects can be divided into smaller parts to support manageable uploads and how incomplete uploads require lifecycle management.

## Roadmap

The current implementation focuses on single-machine object storage. The next phase is exploring distributed storage architecture.

- [ ] Implement consistent hashing for storage-node selection.
- [ ] Build a storage-node registry and placement mechanism.
- [ ] Implement data redistribution when storage nodes are added or removed.
- [ ] Explore replication and recovery from storage-node failures.
- [ ] Improve concurrent write coordination and object consistency.
- [ ] Add automated tests for concurrent uploads and multipart operations.
- [ ] Add benchmarks to measure write throughput and request latency.
- [ ] Implement cleanup of incomplete multipart uploads.

## Project Goals

The goal of this project is to develop a deeper understanding of backend and infrastructure engineering by exploring the problems involved in building an object storage system.

Rather than focusing only on API implementation, the project emphasizes concurrency, filesystem behavior, storage architecture, consistency, and scalability.

## Disclaimer

This project is intended for educational purposes. It is not a production-ready replacement for Amazon S3.
