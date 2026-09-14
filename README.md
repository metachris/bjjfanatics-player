# BJJ Fanatics Courses

Local video player for purchased BJJ Fanatics courses.

## Usage

Open `index.html` in a browser to watch locally downloaded videos.

### URL Parameters

- `series` - Select a series (e.g., `?series=front-headlock`)
- `chapter` - Open a specific chapter (e.g., `?series=front-headlock&chapter=2.3`)

The URL updates automatically when you select a series or chapter, so it can be copied and shared.

### Keyboard Shortcuts

| Key | Action |
|-----|--------|
| Space / K | Play/pause |
| F | Fullscreen |
| T | Theater mode |
| J | Skip back 10s |
| L | Skip forward 10s |
| ← | Skip back 5s |
| → | Skip forward 5s |
| N | Next chapter |
| P | Previous chapter |
| [ | Previous series |
| ] | Next series |
| < | Decrease playback rate |
| > | Increase playback rate |
| 0–9 | Seek to 0%–90% of video |
| / | Search chapters |
| ? | Show keyboard shortcuts |

### Features

- Remembers last watched chapter per series
- Remembers playback position when switching videos or leaving the page
- Theater mode for wider video display

---

## Adding a New Series

### 1. Create the video folder

Create a folder for the series (use kebab-case):

```
mkdir my-new-series
```

Place all `.mp4` video files in this folder. Files should follow the naming pattern:
```
{prefix}-{volume}-{chapter}- {Title}.mp4
```

Example: `1234567890123-01-01- Introduction.mp4`

### 2. Get video durations

Run this command to get all filenames and durations:

```bash
for f in my-new-series/*.mp4; do
  dur=$(ffprobe -v quiet -show_entries format=duration -of csv=p=0 "$f" 2>/dev/null)
  mins=$(echo "$dur / 60" | bc)
  secs=$(printf "%02d" $(echo "$dur % 60 / 1" | bc))
  echo "$(basename "$f")|$mins:$secs"
done
```

### 3. Add series to data.js

Add a new entry to the `SERIES` object in `data.js`:

```javascript
const SERIES = {
    "my-new-series": {
        title: "My New Series Title",
        instructor: "Instructor Name",
        folder: "my-new-series",  // folder name containing the videos
        volumes: [
            {
                title: "Volume 1",
                chapters: [
                    { number: "1.1", file: "1234567890123-01-01- Introduction.mp4", title: "Introduction", duration: "5:30" },
                    { number: "1.2", file: "1234567890123-01-02- Chapter Two.mp4", title: "Chapter Two", duration: "8:45" },
                    // ... more chapters
                ]
            },
            {
                title: "Volume 2",
                chapters: [
                    { number: "2.1", file: "1234567890123-02-01- First Chapter.mp4", title: "First Chapter", duration: "6:15" },
                    // ... more chapters
                ]
            },
            // ... more volumes
        ]
    },
    // ... other series
};
```

#### Field reference

| Field | Description |
|-------|-------------|
| `"my-new-series"` | URL-friendly key used in `?series=` parameter |
| `title` | Display title shown in the player |
| `instructor` | Instructor name shown in the header |
| `folder` | Folder name containing the video files |
| `volumes[].title` | Volume/Part heading in the playlist |
| `chapters[].number` | Chapter number shown in playlist and used in `?chapter=` URL param |
| `chapters[].file` | Exact filename of the video file |
| `chapters[].title` | Chapter title shown in the playlist |
| `chapters[].duration` | Duration in `M:SS` or `MM:SS` format |

### Tips

- The `file` field must match the exact filename (including spaces and special characters)
- Chapter numbers should be unique within a series (e.g., "1.1", "1.2", "2.1")
- Clean up titles by removing redundant prefixes from filenames
- Replace underscores with slashes or appropriate punctuation in titles
