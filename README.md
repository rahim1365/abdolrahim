
#!/usr/bin/env python3
# tools/update_repo_overview.py
import os, re, subprocess, sys
from collections import defaultdict

ROOT = os.path.abspath(os.path.join(os.path.dirname(__file__), ".."))
README = os.path.join(ROOT, "README.md")
START = "<!-- REPO_OVERVIEW:START -->"
END   = "<!-- REPO_OVERVIEW:END -->"

# فایل‌ها/دایرکتوری‌هایی که در نمای کلی نادیده گرفته می‌شوند
IGNORE_DIRS = {".git", ".github", "__pycache__", "node_modules", "dist", "build", ".venv", "venv", ".next", ".turbo"}
IGNORE_FILES = {".DS_Store"}

# تشخیص زبان بر اساس پسوند
EXT_LANG = {
    ".py":"Python",".js":"JavaScript",".ts":"TypeScript",".tsx":"TypeScript",
    ".jsx":"JavaScript",".go":"Go",".rs":"Rust",".java":"Java",
    ".kt":"Kotlin",".cpp":"C++",".cc":"C++",".cxx":"C++",".c":"C",
    ".cs":"C#",".php":"PHP",".rb":"Ruby",".swift":"Swift",".m":"Objective-C",
    ".mm":"Objective-C++",".sh":"Shell",".ps1":"PowerShell",".sql":"SQL",
    ".md":"Markdown",".yml":"YAML",".yaml":"YAML",".json":"JSON",
    ".html":"HTML",".css":"CSS"
}

def file_tree(root):
    lines = []
    for dirpath, dirnames, filenames in os.walk(root):
        # فیلتر دایرکتوری‌ها
        dirnames[:] = [d for d in dirnames if d not in IGNORE_DIRS and not d.startswith(".git")]
        rel = os.path.relpath(dirpath, root)
        depth = 0 if rel == "." else rel.count(os.sep)+1
        prefix = "  " * depth
        if rel != ".":
            lines.append(f"{prefix}📁 {os.path.basename(dirpath)}/")
        for f in sorted(filenames):
            if f in IGNORE_FILES: 
                continue
            if f.startswith(".") and f not in {".env.example"}:
                continue
            path = os.path.join(dirpath, f)
            relp = os.path.relpath(path, root)
            if any(seg in IGNORE_DIRS for seg in relp.split(os.sep)):
                continue
            lines.append(f"{prefix}  └─ {f}")
    return "\n".join(lines)

def count_loc(root):
    lang_loc = defaultdict(int)
    total = 0
    for dirpath, dirnames, filenames in os.walk(root):
        dirnames[:] = [d for d in dirnames if d not in IGNORE_DIRS]
        for f in filenames:
            if f in IGNORE_FILES: 
                continue
            ext = os.path.splitext(f)[1].lower()
            lang = EXT_LANG.get(ext)
            if not lang:
                continue
            path = os.path.join(dirpath, f)
            try:
                with open(path, "r", encoding="utf-8", errors="ignore") as fh:
                    n = sum(1 for _ in fh)
                lang_loc[lang] += n
                total += n
            except Exception:
                pass
    top = sorted(lang_loc.items(), key=lambda x: x[1], reverse=True)
    lines = [f"- **کل خطوط کد:** {total:,}"]
    for lang, n in top[:10]:
        lines.append(f"  - {lang}: {n:,}")
    return "\n".join(lines) if total else "_Lines of code not detected_"

def latest_commit():
    try:
        msg = subprocess.check_output(["git","log","-1","--pretty=%s"], cwd=ROOT, text=True).strip()
        sha = subprocess.check_output(["git","rev-parse","--short","HEAD"], cwd=ROOT, text=True).strip()
        return f"{msg} (`{sha}`)"
    except Exception:
        return "N/A"

def generate_section():
    tree = file_tree(ROOT)
    loc = count_loc(ROOT)
    commit = latest_commit()
    return (
f"""### 📦 Repository Overview

**Latest commit:** {commit}

#### 📁 File tree (trimmed)
{tree}

scss
Copy
Edit

#### 📊 Lines of code by language
{loc}
"""
    )

def replace_between_markers(text, start, end, replacement):
    pattern = re.compile(re.escape(start) + r".*?" + re.escape(end), re.S)
    block = start + "\n\n" + replacement.strip() + "\n\n" + end
    if re.search(pattern, text):
        return re.sub(pattern, block, text)
    return text + ("\n\n" if not text.endswith("\n") else "\n") + block + "\n"

def main():
    if not os.path.exists(README):
        print("README.md not found in repo root.", file=sys.stderr)
        sys.exit(1)
    with open(README, "r", encoding="utf-8") as fh:
        content = fh.read()
    if START not in content or END not in content:
        print("Markers not found in README.md. Add <!-- REPO_OVERVIEW:START --> and <!-- REPO_OVERVIEW:END -->", file=sys.stderr)
        sys.exit(2)
    section = generate_section()
    updated = replace_between_markers(content, START, END, section)
    if updated != content:
        with open(README, "w", encoding="utf-8") as fh:
            fh.write(updated)
        print("README updated.")
    else:
        print("No changes.")

if __name__ == "__main__":
    main()
