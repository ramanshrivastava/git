# Mini-Git: A Learning-Focused Git Implementation

## Overview

This project implements Git from scratch, following the real Git's architecture but simplified for educational purposes. The goal is deep understanding of how Git works internally, not production use.

**Target Size**: 3,000-5,000 lines of C
**Reference**: Git 2.52 (~208,000 lines of C)
**Learning Method**: Historically-grounded, reasoning-based incremental development

## Project Philosophy

- **Learn by Building**: Implement each component from scratch
- **Understand the Why**: Every design decision documented with rationale
- **Historical Context**: Map our implementation to Git's evolution (2005-present)
- **Comparative Analysis**: Explicitly compare mini vs full Git
- **Active Learning**: Checkpoints with exercises and challenges

## Architecture

```
                    Working Directory
                           ↓
                    git add (index)
                           ↓
                      INDEX/STAGE
                           ↓
                    git commit
                           ↓
         ┌─────────────────────────────────┐
         │     OBJECT DATABASE (.git)      │
         │                                 │
         │   ┌──────────────────────────┐ │
         │   │  Objects (content-addr)  │ │
         │   │  - blobs  (files)        │ │
         │   │  - trees  (directories)  │ │
         │   │  - commits (snapshots)   │ │
         │   │  - tags   (markers)      │ │
         │   └──────────────────────────┘ │
         │                                 │
         │   ┌──────────────────────────┐ │
         │   │  References             │ │
         │   │  - branches (refs/heads) │ │
         │   │  - tags (refs/tags)      │ │
         │   │  - HEAD                  │ │
         │   └──────────────────────────┘ │
         └─────────────────────────────────┘
```

## Implementation Status

### Phase 1: Object Model & Storage (In Progress)
- [ ] Commit 1.1: Basic object storage (blob, tree, commit, tag)
- [ ] Commit 1.2: SHA-1 hashing and content addressing
- [ ] Commit 1.3: Object compression (zlib)
- [ ] Commit 1.4: Object reading and parsing

### Phase 2: References & HEAD (Planned)
- [ ] Branch creation and management
- [ ] HEAD pointer
- [ ] Symbolic references
- [ ] Reference resolution

### Phase 3: Index/Staging Area (Planned)
- [ ] Index file format
- [ ] `git add` functionality
- [ ] File status tracking
- [ ] Diff computation

### Phase 4: Core Commands (Planned)
- [ ] `git init` - Repository initialization
- [ ] `git hash-object` - Create objects
- [ ] `git cat-file` - Inspect objects
- [ ] `git ls-tree` - List tree contents
- [ ] `git commit` - Create commits
- [ ] `git log` - Show commit history

### Phase 5: Working Directory (Planned)
- [ ] `git status` - Show working tree status
- [ ] `git diff` - Show changes
- [ ] `git checkout` - Switch branches
- [ ] `git reset` - Reset state

### Phase 6: Advanced Features (Optional)
- [ ] Pack files (compression)
- [ ] Network protocols (fetch/push)
- [ ] Merge algorithms
- [ ] Rebase functionality

## Quick Start

```bash
# Build
cd mini-git
make

# Run tests
make test

# Initialize a repo (once implemented)
./mini-git init
./mini-git add file.txt
./mini-git commit -m "First commit"
```

## Learning Path

1. **Read LEARNING_GUIDE.md** - Understand the pedagogical approach
2. **Review HISTORICAL_TIMELINE.md** - See how Git evolved (2005-present)
3. **Follow the Phases** - Implement incrementally, one commit at a time
4. **Study ADRs** - Understand design decisions (docs/adrs/)
5. **Compare with Git** - Read comparative analysis (docs/comparisons/)
6. **Complete Checkpoints** - Active learning exercises (docs/checkpoints/)

## Documentation Structure

```
docs/
├── adrs/               Architecture Decision Records
│   ├── 001-object-model.md
│   ├── 002-hashing-strategy.md
│   └── ...
├── comparisons/        Mini vs Git comparisons
│   ├── object-storage-comparison.md
│   └── ...
├── diagrams/          Visual architecture
├── checkpoints/       Learning checkpoints with exercises
└── references/        Git internals references, papers
```

## Core Concepts

### 1. Content-Addressable Storage

Git stores everything by the SHA-1 hash of its contents:

```
Content → SHA-1 Hash → Object ID
"Hello" → 5ab2f8a... → .git/objects/5a/b2f8a...
```

### 2. Four Object Types

```c
// blob: File contents
blob 14\0Hello, World!

// tree: Directory structure
tree 73\0100644 file.txt\0<sha1>...

// commit: Snapshot + metadata
commit 180\0tree <sha1>
parent <sha1>
author ...
committer ...

First commit

// tag: Named reference
tag 152\0object <sha1>
type commit
tag v1.0
tagger ...

Release 1.0
```

### 3. References (Branches/Tags)

```
.git/refs/heads/main    →  commit SHA-1
.git/refs/tags/v1.0     →  tag SHA-1 or commit SHA-1
.git/HEAD               →  ref: refs/heads/main
```

### 4. Index (Staging Area)

Binary file tracking:
- File path
- File mode
- SHA-1 of content (blob)
- Metadata (mtime, size, etc.)

## Design Decisions

Key simplifications from Git:

| Aspect | Git | Mini-Git | Rationale |
|--------|-----|----------|-----------|
| Hashing | SHA-1 (moving to SHA-256) | SHA-1 only | Simpler, educational |
| Pack files | Delta compression | Loose objects only | Avoid complexity |
| Network | Smart + dumb protocols | Skip initially | Focus on local ops |
| Merge | Recursive, octopus, etc. | Simple 3-way | Core algorithm only |
| Index | V2, V3, V4 formats | V2 only | Sufficient for learning |
| References | Packed refs, reflogs | Simple files | Easier to understand |

## References

### Git Source Code
This repository! Key files:
- Object model: `/home/user/git/object.h`, `/home/user/git/object.c`
- Blob: `/home/user/git/blob.c`
- Tree: `/home/user/git/tree.c`
- Commit: `/home/user/git/commit.c`
- SHA-1: `/home/user/git/hash.h`
- Index: `/home/user/git/read-cache.c`

### Git Internals Documentation
- [Pro Git Book - Git Internals](https://git-scm.com/book/en/v2/Git-Internals-Plumbing-and-Porcelain)
- Git documentation: `/home/user/git/Documentation/`

### Historical Context
- **Git Birthday**: April 3, 2005
- **Creator**: Linus Torvalds
- **Original Purpose**: Version control for Linux kernel after BitKeeper controversy
- **First commit**: `e83c5163316f89bfbde7d9ab23ca2e25604af290` (April 7, 2005)

### Academic Papers
- "The Design of Git" - various talks by Linus and others
- Content-addressable storage systems
- Merkle trees and hash-based data structures

## Testing Strategy

Unit tests per component:
- Object creation and parsing
- SHA-1 hashing
- Index operations
- Reference management

Integration tests:
- Full workflows (init → add → commit)
- Branch operations
- Object graph verification

Test files (Git operations to replicate):
```bash
# test1: Basic object creation
git hash-object --stdin < file.txt

# test2: Commit creation
git commit -m "Test commit"

# test3: Branch operations
git branch feature
git checkout feature

# test4: History
git log --oneline
```

## Performance Expectations

Git: Highly optimized, handles Linux kernel (millions of objects)
Mini-Git: Correct implementation, not optimized

Focus on correctness first, performance later.

## Common Pitfalls to Avoid

1. **Trying to implement too much at once**
   - Start with basic object storage
   - Add features incrementally

2. **Ignoring Git's actual implementation**
   - Study Git source code
   - Understand why decisions were made

3. **Not testing edge cases**
   - Empty repos
   - Large files
   - Binary files
   - Merge conflicts

4. **Underestimating complexity**
   - Object model is simple
   - Index format is tricky
   - Merge algorithms are complex

## Contributing to Learning

Improvements welcome:
1. Better explanations in ADRs
2. Additional exercises in checkpoints
3. More detailed comparisons
4. Visualization tools
5. Bug fixes with educational value

## License

Educational use. Reference Git's GPLv2 license.

---

**Remember**: The goal is understanding Git internals, not replacing Git!

## What You'll Learn

After completing this project, you'll understand:

- ✅ How Git stores data (content-addressable storage)
- ✅ What blobs, trees, commits, and tags are
- ✅ How Git uses SHA-1 hashing
- ✅ How the index/staging area works
- ✅ How branches are just pointers
- ✅ How commits form a DAG (Directed Acyclic Graph)
- ✅ Why Git is so fast (lazy copying, efficient storage)
- ✅ How to implement version control from scratch

## Next Steps

Ready to start? Begin with:
1. Read `LEARNING_GUIDE.md`
2. Review `HISTORICAL_TIMELINE.md`
3. Start Phase 1.1: Basic Object Storage

Let's build Git from scratch and learn how it works!
