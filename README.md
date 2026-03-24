# Baja Tracker

Track your friends on a custom Baja California map with Book of Mormon locations.

## How It Works

1. **iOS Shortcut** on your friend's phone sends their location to GitHub
2. **GitHub Gist** stores the latest location as JSON
3. **Web App** displays the location on your custom map
4. **GitHub Pages** hosts everything for free

---

## Setup Instructions

### Step 1: Create a GitHub Gist

1. Go to https://gist.github.com
2. Create a new gist:
   - Filename: `location.json`
   - Content:
   ```json
   {
     "name": "Friend Name",
     "lat": 24.0,
     "lng": -110.0,
     "timestamp": "2024-01-01T12:00:00Z"
   }
   ```
3. Click "Create public gist"
4. Copy the Gist ID from the URL (the long string after your username)

### Step 2: Create a GitHub Personal Access Token

1. Go to https://github.com/settings/tokens
2. Click "Generate new token (classic)"
3. Give it a name like "Baja Tracker"
4. Select scope: **gist** (only this one needed)
5. Click "Generate token"
6. **Copy the token now** - you won't see it again!

### Step 3: Configure the Web App

Edit `index.html` and update the CONFIG section:

```javascript
const CONFIG = {
    GIST_ID: 'abc123yourgistidhere',
    GIST_URL: 'https://gist.githubusercontent.com/yourusername/abc123yourgistidhere/raw/location.json',
    // ... rest of config
};
```

### Step 4: Enable GitHub Pages

1. Create a new GitHub repository
2. Upload all files from the `baja-tracker` folder
3. Go to Settings → Pages
4. Source: "Deploy from a branch"
5. Branch: main, folder: / (root)
6. Save
7. Your site will be live at `https://yourusername.github.io/repo-name/`

---

## iOS Shortcut Setup

### Option A: Import Shortcut (Easiest)

1. Open `BajaTracker.shortcut` on your iPhone
2. It will prompt you to enter:
   - Your GitHub username
   - Your Gist ID
   - Your Personal Access Token
   - Your display name
3. Add to Home Screen for easy access

### Option B: Create Manually

1. Open the **Shortcuts** app on iPhone
2. Create a new shortcut
3. Add these actions:

#### Action 1: Get Current Location
- Search: "Get Current Location"
- Tap to add

#### Action 2: Text
- Add a "Text" action
- Paste this template:
```json
{
  "name": "YOUR_NAME",
  "lat": 0,
  "lng": 0,
  "timestamp": ""
}
```

#### Action 3: Replace Text (for latitude)
- Search: "Replace"
- Replace: `0` (first one, for lat)
- With: Select "Latitude" from Current Location variable
- In: Select the Text from previous step

#### Action 4: Replace Text (for longitude)
- Search: "Replace"
- Replace: `0` (for lng)
- With: Select "Longitude" from Current Location
- In: The text

#### Action 5: Replace Text (for timestamp)
- Search: "Replace"
- Replace: `""`
- With: Select "Current Date" (format: ISO 8601)
- In: The text

#### Action 6: Get Contents of URL
- URL: `https://api.github.com/gists/YOUR_GIST_ID`
- Method: **PATCH**
- Headers:
  - `Authorization`: `token YOUR_GITHUB_TOKEN`
  - `Content-Type`: `application/json`
- Request Body: JSON
- Add field: `files`
  - Key: `location.json`
  - Value: `content` = (the text from above)

#### Action 7: Show Notification
- Search: "Show Notification"
- Title: "Location Shared!"
- Body: "Your location has been updated on the map"

### Test the Shortcut

1. Run the shortcut
2. Check your GitHub gist - it should be updated
3. Refresh your web app - you should see the marker!

---

## Calibrating the Map

If the marker appears in the wrong spot, adjust the MAP_BOUNDS in `index.html`:

```javascript
MAP_BOUNDS: {
    north: 32.5,   // Top edge latitude
    south: 22.5,   // Bottom edge latitude
    west: -118.5,  // Left edge longitude
    east: -109.0   // Right edge longitude
}
```

**Tip:** Use Google Maps to find the lat/lng of visible landmarks on your map, then adjust bounds until markers appear correctly.

---

## File Structure

```
baja-tracker/
├── index.html          # Main web app
├── MapVertical.jpg     # The map image
├── README.md           # This file
└── BajaTracker.shortcut # iOS Shortcut (optional)
```

---

## Tips for Your Trip

1. **Add shortcut to Home Screen** for quick one-tap updates
2. **Use automation** to auto-send location every hour:
   - Shortcuts → Automation → Time of Day
   - Run the shortcut automatically
3. **Offline maps:** The map image is cached, but you need internet to send updates
4. **Battery:** Location services use battery - consider a power bank

---

## Troubleshooting

### "No location data yet"
- Run the iOS shortcut first
- Check that your gist URL is correct
- Verify the gist is public

### Marker in wrong place
- Adjust MAP_BOUNDS in index.html
- Make sure lat/lng are correct (lat is north/south, lng is east/west)

### Shortcut fails silently
- Check your GitHub token has `gist` permission
- Verify the Gist ID is correct
- Try the API call manually with curl

---

## Security Notes

- Your gist is **public** by default - anyone with the URL can see locations
- For private tracking, create a private repository and use GitHub's API with auth
- Never commit your GitHub token to a public repository
- Consider using a dedicated GitHub account for tracking

---

## Alternative Hosting Options

- **Render.com:** Drag & drop deploy, auto HTTPS
- **Netlify:** Connect to GitHub, auto-deploy on push
- **Vercel:** Similar to Netlify
- **Cloudflare Pages:** Fast, free, great for static sites

All of these are free for static hosting!
