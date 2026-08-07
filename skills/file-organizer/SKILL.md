---
name: file-organizer
description: Organize files by type, date, or custom rules. Use when user wants to clean up, sort, or organize files and folders.
trigger: \bsắp xếp\b ;; \btổ chức\b ;; \borganize\b ;; \bclean up\b ;; \bdọn dẹp\b ;; \bsort\b ;; \bphân loại\b ;; \bgọn gàng\b ;; \bcategorize\b ;; \bmove file\b ;; \bsắp xếp file\b ;; \bsắp xếp thư mục\b ;; \bsắp xếp folder\b
---

# File Organizer Skill

When organizing files:

1. First, scan the directory with `list_dir` to understand what's there.
2. Categorize files by extension:
   - `.py`, `.js`, `.ts`, `.go`, `.rs` → `code/`
   - `.jpg`, `.png`, `.gif`, `.svg` → `images/`
   - `.pdf`, `.docx`, `.txt`, `.md` → `documents/`
   - `.zip`, `.tar.gz`, `.7z` → `archives/`
   - Others → `misc/`
3. Create the category folders if they don't exist.
4. Move files using the `move` tool.
5. Report a summary of what was moved where.

Always confirm with the user before moving files if the operation is large (>20 files).
