# S3 Clone — Distributed Object Storage in Go

A learning-oriented implementation of an S3-like object storage system built with Go. 
This project explores object storage architecture, concurrent request processing, 
filesystem internals, and scalable storage design.

The primary goal is to understand the engineering challenges behind object storage 
systems, including handling concurrent uploads, maintaining data consistency, 
and efficiently managing objects on disk.

## Features

- **Bucket Management** — Create buckets to organize stored objects.
- **Object Upload** — Upload objects through HTTP APIs.
- **Object Download** — Retrieve stored objects.
- **Multipart Upload** — Upload large objects in multiple parts.
- **Concurrent Write Processing** — Use Go goroutines and channels to process 
  incoming write requests concurrently.
- **Dedicated File Allocation** — Explore allocating separate files to concurrent 
  processing workers to reduce write contention and maintain serialized access 
  to individual files.

## System Design Concepts

This project explores several concepts relevant to storage and infrastructure 
engineering:

- **Concurrency in Go** — Goroutines, channels, and concurrent request processing.
- **Write Serialization** — Coordinating writes to prevent concurrent workers 
  from corrupting shared storage state.
- **Object Storage Architecture** — Organizing buckets, objects, and metadata.
- **Multipart Upload** — Handling large objects through independently uploaded parts.
- **Linux Filesystem Internals** — Understanding inodes, file metadata, and how 
  files are represented and managed by the filesystem.
- **Storage Scalability** — Exploring how storage placement and data redistribution 
  can evolve as the system grows.

## Architecture

The system is designed around an HTTP and gRPC API layer , concurrent request processing, 
object storage, and metadata management.

### Request Flow

1. A client sends an HTTP request to the API server.
2. The server validates the request and identifies the target bucket and object.
3. Upload requests are dispatched to the storage processing layer.
4. Go goroutines process incoming requests, coordinating through channels where 
   appropriate.
5. The storage layer writes object data to the filesystem.
6. PostgreSQL stores the relevant metadata.
7. Download requests retrieve object data through the API server.

*Note: This describes the intended high-level flow. Update it to reflect the 
actual implementation as the architecture evolves.*

## Concurrency and Write Handling

One of the primary learning objectives is understanding how to handle concurrent 
object uploads.

The project explores using Go goroutines and channels to coordinate write 
operations and allocating dedicated files to processing workers.

This approach provides an opportunity to study:

- Concurrent request processing.
- Write contention and serialization.
- Coordination between workers.
- Safe access to shared metadata.
- The trade-offs between parallelism and storage consistency.

Dedicated files can reduce contention when workers write to independent files. 
However, this alone does not guarantee object-level consistency. The implementation 
must also ensure that concurrent requests targeting the same object are handled 
according to a defined consistency policy.

## Multipart Upload

Multipart upload allows a large object to be uploaded as multiple smaller parts.

The workflow includes:

1. Initiating a multipart upload.
2. Assigning an upload ID.
3. Uploading individual parts.
4. Tracking uploaded parts and their metadata.
5. Completing the upload by assembling the parts into the final object.
6. Handling incomplete uploads and abandoned parts.

This feature helps explore large-file handling, upload state management, and 
the lifecycle of temporary storage data.

## Tech Stack

### Client
- React
- Next.js
- Tailwind CSS

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

Explored how goroutines and channels can coordinate concurrent work, distribute 
requests, and reduce contention in storage operations.

### 2. Write Serialization

Learned why concurrent writes require explicit coordination when multiple requests 
can modify the same object or shared metadata.

### 3. Linux Filesystem Internals

Studied how Linux represents files using inodes, which store metadata such as 
file permissions, ownership, timestamps, and references to file data blocks.

This provided a deeper understanding of how object data is persisted on disk 
and how filesystem operations relate to storage-system design.

### 4. Object Storage Design

Explored the separation between object data and metadata, along with the 
challenges of uploading, retrieving, and organizing objects.

### 5. Multipart Upload

Investigated how large objects can be divided into smaller parts to support 
more manageable uploads and how incomplete uploads require lifecycle management.

## Roadmap

- [ ] Implement consistent hashing for storage-node selection.
- [ ] Build a storage-node registry and placement mechanism.
- [ ] Implement data redistribution when storage nodes are added or removed.
- [ ] Explore replication and recovery from storage-node failures.
- [ ] Improve concurrent write coordination and object consistency.
- [ ] Add automated tests for concurrent uploads and multipart operations.
- [ ] Add benchmarks to measure write throughput and request latency.
- [ ] Implement cleanup of incomplete multipart uploads.

## Project Goals

The goal of this project is to develop a deeper understanding of backend and 
infrastructure engineering by exploring the problems involved in building an 
object storage system.

Rather than focusing only on API implementation, the project emphasizes 
concurrency, filesystem behavior, storage architecture, consistency, and 
scalability.

## Disclaimer

This project is intended for educational purposes. It is not a production-ready 
replacement for Amazon S3.
