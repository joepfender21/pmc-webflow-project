# Getting Started with Claude Code for the PMC Webflow Project

## What You're Setting Up

You already have:
- VS Code installed with Claude Code extension
- A Git repo initialized at `pmc-webflow-project`
- An empty `CLAUDE.md` file

What we're doing now:
- Replacing that empty repo with a properly organized project
- Filling `CLAUDE.md` with the rules Claude Code needs
- Getting your existing code into version-controlled files
- Learning the daily workflow

---

## Step 1: Replace Your Current Repo Contents

You have an empty repo with just a blank `CLAUDE.md`. We're going to replace it with the full project I built for you.

### 1a. Download the zip file I've provided

Unzip it somewhere temporary (like your Downloads folder).

### 1b. Open your terminal in VS Code

Click on the **Terminal** tab at the bottom of VS Code (you can see it in your screenshot). You should see something like:

```
Joes-MacBook-Pro:pmc-webflow-project joepfender$
```

That means you're already in the right folder.

### 1c. Delete the old empty CLAUDE.md

Type this and press Enter:
```
rm CLAUDE.md
```

### 1d. Copy all the new files in

Open Finder, navigate to where you unzipped the project, select ALL the files and folders inside, and drag them into the `pmc-webflow-project` folder on your Desktop.

Alternatively, if the unzipped folder is at `~/Downloads/pmc-webflow-project/`, type:
```
cp -R ~/Downloads/pmc-webflow-project/* ~/Desktop/pmc-webflow-project/
cp ~/Downloads/pmc-webflow-project/.gitignore ~/Desktop/pmc-webflow-project/
```

### 1e. Verify the files are there

Type this to see what's in your project now:
```
ls -la
```

You should see: `CLAUDE.md`, `.gitignore`, `global/`, `pages/`, `docs/`

Then check the full tree:
```
find . -type f | head -30
```

You should see files like `./CLAUDE.md`, `./pages/start/head.html`, etc.

---

## Step 2: Commit Everything to Git

This saves a snapshot of your project. Think of it like "saving a version" you can always go back to.

### 2a. Stage all files

```
git add -A
```

This tells Git "I want to save all of these files."

### 2b. Commit with a message

```
git commit -m "initial project setup with full code library"
```

This creates the snapshot. The message in quotes is just a note to yourself about what changed.

### 2c. Verify it worked

```
git log --oneline
```

You should see your commit listed.

---

## Step 3: Configure Your Git Identity (One-Time Fix)

From your screenshot, Git is using your computer name as your identity. Let's fix that:

```
git config --global user.name "Joe Pfender"
git config --global user.email "joe@pfendermarketing.com"
```

This only needs to be done once.

---

## Step 4: Understand the Daily Workflow

Here's how you'll use Claude Code day-to-day. The loop is simple:

### The Loop

1. **Open VS Code** with your pmc-webflow-project folder
2. **Open the Claude Code chat panel** (right sidebar in your screenshot)
3. **Ask Claude Code to do something** (examples below)
4. **Claude Code edits files in your repo** - you can see changes in the editor
5. **Copy the changed file contents into Webflow** (the right code slot)
6. **Publish in Webflow** to see it live
7. **Commit the changes in Git** so you have a history

### Committing After Changes

Every time Claude Code makes changes you're happy with:

```
git add -A
git commit -m "describe what changed"
```

Examples of good commit messages:
- `"added new testimonial card to case-studies results"`
- `"fixed mobile spacing on showcase widget"`
- `"updated form validation error messages"`

---

## Step 5: How to Talk to Claude Code

The Claude Code chat panel works just like regular Claude, but it can see and edit your files. Here are example prompts to get you started:

### Good first task (low risk):
```
Look at pages/case-studies/head.html and tell me what the CSS
for the founding client banner does. Walk me through it section
by section.
```

This doesn't change anything - it just helps you verify Claude Code can read your files.

### Making a change:
```
In pages/case-studies/embeds/results.html, add a third tab called
"Manayunk Barber Studio" with these metrics:
- Google Impressions: 12.4K
- Organic Sessions: 1,847
- Local Search Views: 89K
Follow the exact same HTML pattern as Philly Fades and Arete.
Also update pages/case-studies/body.html so the tab switching JS
handles the new tab.
```

### Fixing a bug:
```
The shimmer animation on the case-studies form card isn't triggering.
Check pages/case-studies/head.html for the .pmc-cs-action CSS
and pages/case-studies/body.html for the IntersectionObserver JS.
The class it should toggle is cs-shimmer-active.
```

### Asking for an explanation:
```
I'm seeing the spotlight effect on /start but not on /case-studies.
Compare the JS in pages/start/body.html vs pages/case-studies/body.html
and tell me what's different about how they target the hero wrapper.
```

---

## Step 6: How to Copy Code Into Webflow

After Claude Code edits a file, here's where each file goes:

| File in repo | Where in Webflow |
|---|---|
| `global/head.html` | Dashboard > Site Settings > Custom Code > **Head Code** |
| `global/footer.html` | Dashboard > Site Settings > Custom Code > **Footer Code** |
| `pages/start/head.html` | Designer > Pages > Start > Settings (gear) > **Inside head tag** |
| `pages/start/body.html` | Designer > Pages > Start > Settings (gear) > **Before body tag** |
| `pages/start/embeds/lead-form.html` | Designer > Navigate to the embed element > **Paste as HTML embed** |
| (Same pattern for every other page) | |

### The copy-paste process:

1. In VS Code, open the file Claude Code changed
2. Select All (Cmd+A) and Copy (Cmd+C)
3. Go to the right place in Webflow
4. Select All the existing code there and Paste (Cmd+V) to replace it
5. Save and Publish

---

## Step 7: The Golden Rules

1. **Never edit code directly in Webflow anymore.** Always edit in VS Code (via Claude Code), then copy to Webflow. This keeps your repo as the source of truth.

2. **Commit before and after major changes.** If something breaks, you can always go back:
   ```
   git log --oneline
   git checkout [commit-hash] -- pages/case-studies/head.html
   ```
   (That last command restores a specific file to a previous version.)

3. **Always give Claude Code the full context.** Instead of "fix the spacing," say "the metrics cards in pages/case-studies/embeds/results.html have too much gap between them on mobile. Check the responsive CSS in pages/case-studies/head.html at the 640px breakpoint."

4. **CLAUDE.md is your leverage.** When Claude Code gives you code that breaks the rules (like putting CSS in an embed), just tell it: "Read CLAUDE.md - embeds are HTML only." It will correct itself.

---

## Quick Reference: Terminal Commands You'll Use

| What you want to do | Command |
|---|---|
| See what folder you're in | `pwd` |
| List files in current folder | `ls` |
| See what Git thinks changed | `git status` |
| Save all changes | `git add -A && git commit -m "your message"` |
| See commit history | `git log --oneline` |
| Undo uncommitted changes to a file | `git checkout -- path/to/file.html` |
| See what changed in a file | `git diff path/to/file.html` |

---

## What's Next

Your immediate next task should be:

1. Get this project structure into your repo (Steps 1-3 above)
2. Do the "good first task" in Step 5 to verify Claude Code can read your files
3. Pick a real task on /case-studies and try the full loop

You've got the documentation, the architecture, and now the tooling. Time to build.
