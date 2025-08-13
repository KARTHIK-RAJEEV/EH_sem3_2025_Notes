### ✅ Script Snippet

```bash
while read line; do
  echo "$line" >> "$LOGFILE"
  
  # Commit and push each time a new line is added
  cd "$LOGDIR"
  git add download_changes.log
  git commit -m "Update log at $(date '+%Y-%m-%d %H:%M:%S')" >/dev/null 2>&1
  git push origin main >/dev/null 2>&1
done
```

### ⚠️ Requirements:

  ```bash
  cd ~/logs
  git init
  git remote add origin <your-repo-url>
  git checkout -b main
  git add download_changes.log
  git commit -m "Initial commit"
  git push -u origin main
  ```

---

## 🌐 2. Upload to a Server via SCP
```bash
scp "$LOGFILE" user@yourserver.com:/path/to/remote/
```



---

## 🆙 3. Upload to Cloud (Google Drive/Dropbox)

* `rclone` (for Google Drive/Dropbox/OneDrive)
* or mounting cloud storage with `gvfs`

---

## ✅ Final Working Script:

```bash
#!/bin/bash

WATCHDIR="$HOME/Downloads"
LOGDIR="$HOME/logs"
LOGFILE="$LOGDIR/download_changes.log"

mkdir -p "$LOGDIR"

echo "Monitoring: $WATCHDIR"
echo "Logging to: $LOGFILE"
echo "Watches established."

inotifywait -mrq -e create,delete,modify "$WATCHDIR" --format '[%T] %e %w%f' --timefmt '%Y-%m-%d %H:%M:%S' |
grep --line-buffered -v "$LOGFILE" |
while read line; do
  echo "$line" >> "$LOGFILE"
  
  cd "$LOGDIR"
  git add download_changes.log
  git commit -m "Update log at $(date '+%Y-%m-%d %H:%M:%S')" >/dev/null 2>&1
  git push origin main >/dev/null 2>&1
done
```
