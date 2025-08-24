
mport requests, os, re, sys

README = "README.md"
START = "<!-- STATS:START -->"
END   = "<!-- STATS:END -->"

USER = os.getenv("GITHUB_REPOSITORY", "USERNAME/REPO").split("/")[0]
REPO = os.getenv("GITHUB_REPOSITORY", "USERNAME/REPO").split("/")[1]

def get_stats(user, repo):
    url = f"https://api.github.com/repos/{user}/{repo}"
    r = requests.get(url)
    if r.status_code != 200:
        print("GitHub API error:", r.status_code, file=sys.stderr)
        return None
    data = r.json()
    stars = data["stargazers_count"]
    forks = data["forks_count"]
    watchers = data["watchers_count"]
    return f"""
### 📊 GitHub Repository Stats
- ⭐ Stars: **{stars}**
- 🍴 Forks: **{forks}**
- 👀 Watchers: **{watchers}**
"""

def replace_between(text, start, end, replacement):
    block = start + "\n" + replacement.strip() + "\n" + end
    pattern = re.compile(re.escape(start) + r".*?" + re.escape(end), re.S)
    return re.sub(pattern, block, text)

def main():
    if not os.path.exists(README):
        print("README.md not found")
        sys.exit(1)
    with open(README, "r", encoding="utf-8") as f:
        content = f.read()
    stats = get_stats(USER, REPO)
    if not stats:
        sys.exit(1)
    updated = replace_between(content, START, END, stats)
    if updated != content:
        with open(README, "w", encoding="utf-8") as f:
            f.write(updated)
        print("README updated with repo stats")

if __name__ == "__main__":
    main()
